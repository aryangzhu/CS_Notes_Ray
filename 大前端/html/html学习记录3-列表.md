### 列表

####　有序列表　

ol　应用范围：试卷,问答

有两个属性

1.start=xx 表示从xx开始计数

2.type 显示类型 A a I i

```html
<ol>
        <li>Java</li>
        <li>python</li>
        <li>运维</li>
        <li>前端</li>
        <li>C/c++</li>
</ol>
```

#### 无序列表　

ul　应用范围：导航,侧边栏

```html
<ul>
    <li>Java</li>
    <li>python</li>
    <li>运维</li>
    <li>前端</li>
    <li>C/c++</li>
</ul>

在这里强调一下
<u></u>下划线
<b></b>加粗
<i></i>斜体
其实就是对应意思的英文缩写
```
#### 自定义列表

dl:标签
dt:列表名称
dd:列表内容

```html
<dl>
    <dt>学科</dt>
    <dd>Java</dd>
    <dd>Python</dd>
    <dd>Linux</dd>
    <dd>C</dd>
</dl>
```