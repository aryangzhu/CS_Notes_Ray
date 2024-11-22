当一个页面由多个页面组成时,我们需要使用frameset标签(现在已经淘汰)

在一个网页中嵌入一个小的网页使用iframe

### 页面结构

​	header--标题头部区域的内容
​    footer--标记脚部区域的内容
​    section--Web页面中的一块独立区域
​    article--独立的文章内容
​    aside--侧边栏
​    nav--导航类辅助内容

###s 内联框架

```html
<frameset rows="20%,*" >  
	<frame src="frames/top.html"></frame>
</frameset>
```
```html
<iframe src="" name="hello" frameborder="0" 			width="1000px" height="800px">
<iframe>
```



```html
<!--<iframe src="//player.bilibili.com/player.html? 			aid=55631961&bvid=BV1x4411V75C&cid=97257967&page=11"-->
    <!--scrolling="no"-->
    <!--border="0"-->
    <!--frameborder="no"-->
    <!--framespacing="0"-->
    <!--allowfullscreen="true">-->

<!--</iframe>-->
```