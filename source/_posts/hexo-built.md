---
layout: post
title: "使用Hexo搭建你的溫馨部落格"
date: 2018-01-29 0:53
comments: true
tags: 
	- Hexo
---
> 此文介紹使用Hexo + Github架構搭建個人博客的詳細步驟，我儘量做到能讓非專業的人也能聽懂，因為有一個精美的個人博客是大家都希望的。

**申明**：文章主要參考了潘柏信的相關文章，如果覺得文章前部分有些類似也不要太驚訝，原因有二，其一，Hexo搭建很簡單，所以內容類似很正常。其次我搭建博客參考的也是他的文章。但是後部分的域名、主題等就沒參考他了。

## 簡介

Hexo是一個簡單、快速、強大的基於 Github Pages的博客發佈工具，支持Markdown格式，有眾多優秀的插件和主題。這個之前的文章的已經提及，Github託管的好處是方便快捷，不需要自己額外搭服務器，另一個好處是使用Github託管可以免去域名審核。什麼是域名審核？如果你的博客不打算使用自己的域名，只使用Github提供的鏈接的話就沒有這個問題。但是如果大家想要使用自己的域名的話，現在國家在這方面管得緊，需要把你的域名和服務器備案，不過Github服務器在國外，所以我們就免去了備案的麻煩。

<!--more-->

## 正文

現在就開始嘍，對了，這個教程針對的是Mac用戶，Windows用戶使用問題應該不大，但是我沒有試過。

- **安裝Node**
Node的作用是生成靜態頁面。[Node.js](https://nodejs.org/en/)官網下載安裝即可，安裝步驟百度上海量，此處不詳細描述。

- **安裝Git**

Git是版本控制工具，非專業人士可能沒有提說過，如果電腦中有安裝Xcode的話默認已經安裝了Git。如果沒有，Git安裝也是很簡單的，百度（能百度的不叫問題，哈哈）即可。

- **申請Github帳號**

因為我們的博客是部署在Github上的，所以當讓要有一個Github帳號了。至於本地Git和Github關聯的問題，自行百度吧。這些對於專業人士來說都不是問題，非專業人士的話扣我吧。。。

- **安裝Hexo**

```$ sudo npm install -g hexo```

- **初始化博客**

這個過程是真正開始建立你的博客了。

在本地建立文件夾，這個文件夾就是你的博客倉庫，名字任意起，比如Blog，進入文件夾，並執行hexo init 命令。

```shell
$ mkdir Blog
$ cd Blog
$ hexo init
```

然後你的博客就建好了，是不是很簡單？

- **生成靜態頁面**

使用hexo g 命令生成靜態頁面。

```
$ hexo g(或者hexo generate)
```

- **本地啟動**

啟動本地服務器，本地查看你的博客，用來調適。

```
$ hexo s(或者hexo server)
```

瀏覽器輸入[http://localhost:4000，即可查看。](http://localhost:4000，即可查看。/)

關閉server，使用組合鍵ctrl+c



### **備置Github託管**

- **建立託管倉庫（Repository）**

倉庫名必須是【your_user_name.github.io】，比如我的Github帳戶名是lixiao1k，那我就必須起名為lixiao1k.github.io。然後和本地的博客倉庫建立關聯。

具體關聯步驟如下。

打開倉庫文件夾下的配置文件_config.yml ，具體結構如下

```
Blog 
　｜ 
　｜－－ _config.yml 
　｜－－ node_modules 
　｜－－ public 
　｜－－ source 
　｜－－ db.json	
　｜－－ package.json 
　｜－－ scaffolds 
　｜－－ themes
```

然後將配置文件中的Deploy相關字段修改如下：

```
deploy:
  type: git
  repository: https://github.com/your_user_name/your_user_name.github.io.git
  branch: master
```

執行一下命令才能使用Git部署：

```
$ npm install hexo-deployer-git --save
```

然後執行命令

```
$ hexo deploy
```

就完成了部署，然後打開http://your_user_name.github.io/就可以看到你的博客了。

- **添加博文**

博文的添加很簡單，只需要把文章放在source/_posts文件夾下即可，文章支持Markdown各式。然後生成靜態頁面並部署，具體命令如下：

```
$ hexo clean
$ hexo generate
$ hexo deploy
```

也可以簡單的用一條命令

```
$ hexo clean && hexo g && hexo d
```

然後就可以看到你的博客內容更新了。

- **如何區分標題和全文**

一開始會出現這樣的問題，就是博客在顯示所有文章時，我們期望每篇文章只顯示標題和簡介部分，全文內容應該隱藏。這個很簡單，只需要在文章中加入註釋，就可以區分開標題和全文內容了。



### 使用主題

使用Github託管建博客時，還有另外一個博客發佈工具叫做JkeyII，Jkeyll相對於Hexo有許多優點，Github官方也推薦使用Jkeyll，但是Jkeyll的主題沒有Hexo多，這也是為什麼目前為止大多數人還是選擇使用Hexo，這充分說明了語言或者工具的社區之重要性。

Hexo主題有很多，[主题列表](https://github.com/hexojs/hexo/wiki/Themes)中列出了大多數主題，我選用的主題是Yilia，接下來我也來講解一下如何使用Yilia主題。大家可以選取其他主題，當然也可以和我的一樣，這樣也就證明我的審美還是不錯的😄

[Yilia](https://github.com/litten/hexo-theme-yilia)主題傳送門

作者做了很詳細的介紹了。

- **安裝：**

```$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

- **配置：**

修改hexo根目录下的字段 ，設為yilia：`_config.yml` ： `theme: yilia`

- **更新**

```
$ cd themes/yilia
$ git pull
```

- **注意**

作者說配置是在主目錄下的_config.yml，容易被誤解，不是你博客的主目錄，是主題yilia主目錄下的_config.yml，這樣才會有用。

- **PS**

如果你不喜歡主題代碼顯示的亮黑色，可以修改themes/yilia/source/下的main.0cf68a.css文件的相關字段。查找.article-entry .highlight，將顏色改為自己喜歡的。



### 綁定個人域名

自己的博客當然要用自己的域名了，不能總是在域名後面帶著github.io的小尾巴吧，多難看啊。

这个步骤终于要Money了。不过比大家想象的要便宜。

购买域名很便宜，贵的主要的是解析域名服务，怎么说？就是我们的域名到IP地址之间是需要有一个转换的过程的，网络上定位主机用的是IP地址，但是IP地址不方便人们记忆，所以出现了个性化的域名。这时中间就需要有一个域名解析服务器（DNS）的存在，来将域名解析成相应的IP地址，这个服务的收费就比较昂贵了。但是，但是！相对我们这种小众使用是有免费的！推荐大家使用DNSPod网站，这是提供免费智能DNS解析服务的。来，传送门在此[DNSPod](https://www.dnspod.cn/)。首先註冊一個帳號，然後買個域名，此網站買域名是會鏈接到騰訊。現在知道為什麼免費了吧。不過騰訊的域名也不貴，我買的cn域名11元/年。相比於其他數字產品要便宜多了。

- **購買域名**

買好了域名，然後實名認證，此處不需要備案，應為只有在中國大陸地區開展網絡服務的才需要備案，Github的服務器在國外，所以我們不需要備案。Github在國內可以直接訪問，曾有段時間Github也被封了，後來友解封了。相信我，Github以後也不會被封，除非中國政府不想發展信息技術了。

- **添加CNAME文件**

然後想你的博客所在主目錄下的source文件夾下創建CNAME文件，文件內容寫

```
example.com
```

也就是你註冊的頂級域名。

然後部署到Github上，命令如下：

```
`$ hexo g && hexo d`
```

- **DNS配置**

就是在你的騰訊控制台下，找到你買的域名，然後域名解析，添加三條紀錄：

```
@          A             192.30.252.153
@          A             192.30.252.154
www      CNAME           username.github.io.
```

用你的Github帳戶名替換其中的username

然後應該DNSPod中就應該就有相應的紀錄了，沒有的話先等10分鐘，還沒有就直接在DNSPod中添加紀錄。等待10分鐘後，瀏覽器里輸入你的域名就可以看到你的博客了！



### 增加阅读统计功能

Hexo和Yilia都沒有自帶的統計功能，Yilia作者聲稱不會增加此項功能，因為他認為博客的訪客量給顯示讀者看的價值不大。對此我不敢苟同，博客的訪客量反映的是博文的質量，如果把訪客量顯示與讀者看，可以起到激勵作者的功能。

既然沒有現成的統計功能，我們就只能尋求插件幫助。目前比較流行的是“不蒜子”插件，安裝簡單便利。

首先

- **安裝“不蒜子”腳本**

在`themes\yilia\layout\_partial\after-footer.ejs`（注意yilia是我的主題名，不同的主題更改位址需要博主靈活變更）最後添加

```
<script  async  src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

然後

- **添加網站總訪問量和訪問人次**

修改`themes\yilia\layout\_partial\footer.ejs`，加入訪問量和訪問人次的代碼

```
<span id="busuanzi_container_site_pv"> 本站总访问量
  <span id="busuanzi_value_site_pv"></span>次
</span>
<span id="busuanzi_container_site_uv">  本站访客数
  <span id="busuanzi_value_site_uv"></span>人次
</span>
```

最後，如果需要為每篇博文都添加訪問量

- **添加單篇文章點擊量**

在編輯文章時，於需要添加的部位加入代碼

```
<span id="busuanzi_container_page_pv">  本文总阅读量
  <span id="busuanzi_value_page_pv"></span>次
</span>
```





### 添加Gitment評論功能

設置Gitment評論功能異常艱辛，踩了很多坑，主要還是因為很多使用細節作者沒有交代清楚。

Gitment是[imsun](https://imsun.net/posts/gitment-introduction/)實現的一款基於Github Issues的評論系統。支持在前端直接引入，不需要任何後端代碼。可以在頁面進行登錄、查看、評論、點贊等操作，同時有完整的Markdown/GFM和代碼高亮支持。尤為適合各種基於Github Pages的靜態博客或項目頁面。詳情請看imsun主頁。

具體操作步驟如下：

- **註冊OAuth Application**

[註冊入口](https://github.com/settings/applications/new)

註冊新的OAuth Application的各字段填寫提示如下：

`Application name` 應用名稱隨便填寫，一般選擇your_user_name.github.io

`Homepage URL` 填寫主頁地址，注意要詳細填寫，如`http://sheltonlee.cn/`而不要寫`sheltonlee.cn`。還沒有個人域名的同學可以寫`https://your_user_name.github.io`。

`Application description` 隨便寫

`Authorization callback URL` 回調地址填寫你的主頁地址，同`Homepage_URL`

註冊成功後獲取你的`Client ID` 和`Client Secret`

 

- **主題中引入Gitment**

在主題的配置文件中引入Gitment，以Yilia為例，修改`themes\yilia\_config.yml` 中的Gitment配置字段：

```
#5、Gitment
gitment_owner: lixiao1k     #你的 GitHub ID
gitment_repo: 'lixiao1k.github.io'          #存储评论的 repo
gitment_oauth:
  client_id: 'xxxxxxxxxxxxxxxxxxxx'           #client ID
  client_secret: 'xxxxxxxxxxxxxxxxxxxxxxxxxx'       #client secret
```

然後部署，就有了評論功能

- **初始化評論**

頁面發佈後，你需要訪問頁面並使用你的Github帳號登錄，點擊初始化按鈕。



### 踩過的坑😢

各種問題可以參考[iHTCboy](https://www.jianshu.com/p/57afa4844aaa)，我這裡講一些我自己踩的坑，也就是網上沒有任何解決方法的坑。

- **Error: Not Found問題**

一般是owner或者repo配置錯誤了，注意名字和倉庫名字的大小寫。

然而，還是有可能依舊解決不了這個問題，[Yilia](https://github.com/litten/hexo-theme-yilia) 倉庫的Issues便有許多人遇到這樣的問題始終解決不了，然而並沒有人能給出有效解決辦法，一開始我碰到這個問題時也很頭大，總共就兩個字段配置，寫錯的可能性很低，說明問題不在這個上面。Gitment根據你配置的owner和repo去定位你的倉庫，如果找不到，只有三種原因，一、倉庫本身不存在，二、owner和repo配置錯誤，三、你把倉庫設置為private了！！！。除此三種，如果你還能找出其他原因的話，來南京我請你吃飯。我的問題就是手欠把倉庫設成了private，改為public就行了。

- **Error：validation failed**

這個問題[iHTCboy](https://www.jianshu.com/p/57afa4844aaa)有詳細解答，並且還有很好的解決方案。這位博主提出在`themes\next\layout_third-party\comments\gitments.swig` 中修改，將

```
id: '<%= page.title %>'
```

改為

```
id: '<%= page.date %>'
```

這麼改的是因為在issues里面，可以发现是根据网页标题来新建issues的，而issue的标签label有长度限制！labels的最大长度限制是50个字符，換成以日期為ID就可以避免這個問題。

Over!

歡迎提問喔。

喜歡的點個讚，你的讚是我前進的動力，謝謝大家！