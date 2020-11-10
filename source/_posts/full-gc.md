---
layout: post
title: "HiveServer Full GC 問題解決"
date: 2018-08-11 14:48
comments: true
tags: 
	- Hive
---

> 此文介紹公司大數據平台HiveServer出現過的Full GC問題的現象和原因，以及解決方法。
>
> 通過修改Hive源碼實現需求。

<!-- more -->

## 背景

公司在3月16日出現HiveServer任務阻塞，並且導致HiveServer節點自動重啓的現象。線上任務大量卡死。阻塞的HiveServer節點均出現Full GC現象，內存資源完全撐爆。同時不正常的Map任務直接導致了HDFS大量文件的鑒權執行，間接導致HDFS節點癱瘓。

## 根因

通過分析運行日誌發現，出現Full GC的Server節點都有Number of Splits：XXXXX紀錄。這裡記錄的是Hive任務MapReduce階段產生的分片數量。導致集群卡死的任務產生的splits數達到了驚人的600多萬，而正常業務任務的splits數應該保持在1萬左右。很顯然這是不正常的SQL請求導致的服務器卡死，可能是業務人員SQL沒有經過優化。介於此我們必須採取方式來避免這種情況的發生，對於不正常的Hive任務我們應該拒絕執行。

在執行MapReduce任務之前，我們需要分析Table表文件存儲地址的所有文件，根據文件大小或者文件數量（目前默認採用的是文件大小）來產生所有的splits分片，然後根據分片數劃分同等數量的Map，將每個split分配給一個Map進行處理。

Hive默認調用org.apache.hadoop.hive.ql.io.ComineHiveInputFormat.java的getSplits方法來處理分片，ComineHiveInputFormat類在分片時進行組合優化，將一個Table的同一個Partition中的小文件組合分片，多少小文件組合在一個分片中由小文件的大小和分片設定大小等參數共同決定。我們也可以在執行Hive任務之前通過命令`set hive.input.format;`來設置其他的inputformat方式來處理分片。比如通過命令

```shell
set hive.input.format=org.apache.hadoop.hive.ql.io.HiveInputFormat;
```

我們可以把inputformat設定為HiveInputFormat，這樣分片時就不會優化，hive任務需要訪問多少HDFS文件就會產生多少分片以及多少Map數。

不管是哪個InputFormat，getSplits函數時會把生成的所有splits都保存在內存中，這樣如果遇到大規模splits時必然會導致內存Full，然我們已ComineHiveInputFormat為例：

```java
@Override
  public InputSplit[] getSplits(JobConf job, int numSplits) throws IOException {
    ......
    ArrayList<InputSplit> result = new ArrayList<InputSplit>();
    ......
    if (combinablePaths.size() > 0) {
    ......
      InputSplit[] splits = getCombineSplits(job, numSplits, pathToPartitionInfo);
      for (InputSplit split : splits) {
        result.add(split);
      }
    }
    ......
    LOG.info("Number of all splits " + result.size());
    perfLogger.PerfLogEnd(CLASS_NAME, PerfLogger.GET_SPLITS);
    return result.toArray(new InputSplit[result.size()]);
  }
```

我們可見getSplits方法直接把所有生成的splits放入result中，這直接導致了內存資源的大量佔用。

## 解決辦法

方案是為管理員提供可以設置splits最大上限的通道，對於所有Hive任務執行前，我們事先判斷Splits數是否會超出最大上限，超過則報出異常，不予執行。

