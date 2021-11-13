### 相对定位

```
   <!--相对定位
    相对于自己原来的位置进行偏移-->
    <style>
        body{
            padding:20px;
        }
        div{
            margin:10px;
            padding:5px;
            font-size:12px;
            line-height: 25px;
        }
        #father{
            border:1px solid #666666;
            padding:0;
        }
        #first{
            background-color: #aa1133;
            border:1px dashed #b27530;
            position:relative;
            top: -3px;
            left: 7px;
        }
        #second{
            background-color: #255066;
            border:1px dashed #255066;
        }
        #third{
            border:1px dashed #1c6615;
            background-color: #1c6699;
        }
    </style>
</head>
<body>
<div id="father">
    <div id="first">第一个盒子</div>
    <div id="second">第二个盒子</div>
    <div id="third">第三个盒子</div>
</div>
</body>
```
### 绝对定位
#### 1.父级元素存在，就是相对于父元素的位置
#### 2.父级元素不存在，相对于浏览器进行定位
### 固定定位

```
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body{
            height: 1000px;
        }
        div:nth-of-type(1){/*绝对定位：相对于浏览器*/
            width: 100px;
            height: 100px;
            background:red;
            position:absolute;
            right: 0;
            bottom: 0;
        }
        div:nth-of-type(2){/*fixed,固定定位*/
            width:50px;
            height:50px;
            background:yellow;
            position:fixed;
            /*right:0;*/
            /*left: 0;*/
            bottom:0;
        }
    </style>
</head>
<body>
<div>div1</div>
<div>div2</div>
</body>
```

### 总结

**1、relative：相对于原来位置移动，元素设置此属性之后仍然处在文档流中，不影响其他元素的布局**

![image-20211024215647086](/home/leiliu/.config/Typora/typora-user-images/image-20211024215647086.png)

**2、absolute:元素会脱离文档流，如果设置偏移量，会影响其他元素的位置定位**

![](https://gitee.com/aryangzhu/picture/raw/master/%E9%80%89%E5%8C%BA_264.png)

**父元素设置了相对定位或绝对定位，元素会相对于离自己最近的设置了相对或绝对定位的父元素进行定位（或者说离自己最近的不是static的父元素进行定位，因为元素默认是static）**

