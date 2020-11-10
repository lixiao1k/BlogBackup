---
layout: post
title: "使用Matplotlib畫圖小敘"
date: 2018-01-28 14:48
comments: true
tags: 
	- python
	- matplotlib
---

> 最近復現一篇論文，論文中有許多的可視化做圖，主要畫一些折線圖和柱狀圖，用的是R語言，我都用Python來實現了。咱先不論R語言和Python那個好，因為一旦開口盆友圈那些用R語言的定饒不了我。態度很明確了吧，逃～～

<!-- more -->

我是在實驗室工作時首次接觸了Matplotlib，由於實驗室的所有工作內容是要嚴格保密的，所以在這裡我這裡只能把其中運用到的一些純技術抽離出來，細細品嚼。但是缺少了實驗內容的大環境，可能會出現些上下文銜接不暢、有骨無肉的不快感。

## 折線圖

畫普通的折線圖很簡單，這裡就不多說了。但是有一些特殊類型的折線圖起初卡了我一段時間。

- **一圖多線**

關鍵點在於如何設置雙座標軸，這裡使用到了twinx和twiny方法，twinx制作共享x轴的第二个轴。 新轴将覆盖ax（如果ax为None，则为当前轴）。 ax2的标记将被放置在右侧，并且ax2实例被返回。twiny同理。

首先獲取圖的句柄，然後設置此圖的另一座標軸。代碼如下：

```python
fig = plt.figure()
ax1 = fig.add_subplot(111)
l1, = ax1.plot(x, y1, 'b')
ax2 = ax1.twinx()
l2, = ax2.plot(x, y2, 'r')
```

l1, l2, 在這裡顯得很突兀，其實不然，這裡獲取的來兩個折線句柄是用來設置圖例的。

```python
plt.legend([l1, l2], ['Not Seasonally Adjusted', 'Seasonally adjusted'], loc=3, 
           fontsize='x-small')
```

legend方法（當然有很多種用法）傳入的參數首先是一個線條list，然後這些線條對應的label list。其他一些參數是外觀調節的。

設置座標軸的刻度標籤方法（一目了然不解釋）：

```python
ax2.set_xticks([0, 24, 48, 72, 96, 120, 144, 168])
ax2.set_xticklabels(['2003', '2005', '2007', '2009', '2011', '2013', '2015', '2017'])
```

- **點線圖**

廢話不多說，放圖

![matplotlib-two](/photos/matplotlib-draw/matplotlib-two.png)

點線圖畫起來很簡單，但是裡面藏著的知識點是，你畫的圖是可以疊加的，就像塗層一樣。

```
plt.scatter(e[:-3], b, color='blue') # 前面兩個參數分別是點的橫縱座標
plt.plot(e[:-3], regr.predict(a), color='red', linewidth=4)
```

- **X-軸取值範圍不同的圖，典型的預測圖**

依舊先放圖

![matplotlib-three](/photos/matplotlib-draw/matplotlib-three.png)

一開始想說使用set_xlim方法來限制另一條線的x軸範圍。其實是行不通的，兩條線既然已經共用了一個座標軸，我們是沒辦法單獨調節一張圖的x-軸的顯示範圍。因為在一個座標軸裡畫的多條線的數據緯度必須是一樣的，據個例子：

```python
plt.plot(x, train_data)
plt.plot(y, predict_result)
```

想要用這種方法畫預測線和訓練線，當然此時的預測線數據緯度是小於訓練線的。想著然後用set_xlim方法來調節訓練線在x-軸上的位置。喔，好天真啊。應為圖根本就畫不出來的，哈哈哈。在同一個座標系裡做圖，有一個數據緯度的限制，首先橫縱座標數據未讀相同是不必多說的，其次多條線的數據緯度也要相同。以上面這個例子來說就是 len(x) = len(train_data) = len(y) = len(predict_result)， 然而我們的len(train_data) != len(predict_result)。所以此路不通。那應該用什麼辦法呢？

解決的辦法還是使用twinx，twinx簡直就是神器啊。使用了twinx兩條線可以共用x軸，但是座標軸對象不是一個，他們只是共用了twinx，畫圖時就可以互相不影響了。

圖中的虛線和提示很簡單，兩條代碼搞定。

```python
plt.plot([60, 60], [0, 1400], color ='black', linewidth=2.5, linestyle="--")
plt.annotate('left is training data',
	xy=(60, 1000), xycoords='data',
	xytext=(+10, +30), textcoords='offset points', fontsize=12,
	arrowprops=dict(arrowstyle="-", connectionstyle="arc3"))
```

## 柱狀圖

![matplotlib-four](/photos/matplotlib-draw/matplotlib-four.png)

這張柱狀圖和之前討論到的圖比較有兩點特色，一個是兩份數據的條狀圖形在座標上的分佈位置，一個如何處理過長的刻度標籤。

首先我們來看畫一類數據的柱狀圖（這裡就不具體講了）。可以很容易的看到，只有一類數據的柱狀圖的條狀圖形是系統自動設置在座標中的位置和寬度（當然這個寬度可以用戶自定義）的。

引伸到畫多類數據時，我們只需要定義好一個數據點的條狀圖形簇的位置，然後再以條狀圖形簇為標準微調各類數據的條狀圖形的各自所在的點。語文學的不好，不能表述清楚，見諒。代碼貼出來就一目了然了

```python
size = len(predict_data1) # 數據緯度
x = np.arange(size) #條狀圖形簇定標點
total_width, n = 0.8, 2
width = total_width / n # 用來設置每條條狀圖的位置
x = x - (total_width - width) # 通上
fig = plt.figure()
ax = fig.add_subplot(111)
p1 = plt.bar(x, predict_data1, width=width, color='r')
p2 = plt.bar(x + width, predict_data2, width=width)
```

處理過長的刻度標籤一般採用使標籤傾斜的方法，直接設置set_xticklabels方法中的rotatio參數即可。

暫結。

謝謝，希望大家提出寶貴意見。

