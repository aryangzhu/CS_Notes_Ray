#### １．图像标签
​	`<`!--img学习
​       src:图片地址
​       相对地址,绝对地址
​    -->`
​	 <img src="../resource/img/1.png" 　alt="狂神头像" title="悬停文字" 　width="300" height="300" hidden>`
#### ２．链接标签
​	１.a标签
​	<!--a标签
​	href：必填,表示要跳转到哪个页面
​	target:表示窗口在哪里打开
​    _blank:在新标签页中打开
​    _self:在自己网页中打开
​	-->

```html
<a href="demo1.html" target="_blank">点击跳转</a>
<a href="https://www.baidu.com" target="_self">点击跳转到百度　</a>
２．图像超链接
<a href="demo1.html">
<img src="../resource/img/1.png" 　alt="狂神头像" 　title="悬停文字" 　width="300" height="300"></a>
３．锚链接
<!--锚链接
1.需要一个锚标记
2.跳转到标记-->
<a name="top">顶部</a><br>
<a href="#top">回到顶部</a>
４．功能性链接(非常有用)
<a href="mailto:1935314139@qq.com">点击联系我</a>
<a target="_blank" href="http://wpa.qq.com/msgrd?v=3&uin=24736743&site=qq&menu=yes"><img 			border="0" src="http://wpa.qq.com/pa?p=2:1935314139:51" alt="点击这里给我发消息" title="点击这里给我发消		息"/></a>
```
#### ３．总结
​	块元素
​    	无论内容多少,该元素独占一行
​        例如p、h1-h6
​    行内元素
​    	内容撑开宽度,左右都是行内元素的可以排在一行
​        例如a、strong、em