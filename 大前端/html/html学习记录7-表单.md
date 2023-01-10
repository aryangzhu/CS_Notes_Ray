## 表单
表单form

action:点击submit类型按钮时表单提交的位置,可以是网站,也可以是一个请求处理的地址

method:post,get 提交方式

get方式：我们可以看到url中提交的信息,不安全,高效

post方式：比较安全,传输文件
        

```html
    <form  action="demo1.html" method="post">
    <!--文本输入框：input type="text"-->

    <p>名字：这里用了只读属性</br>
        <input type="text" name="username" value="admin" readonly/>　</p>
    <!--密码框-->

    <p>密码：这里用了隐藏--hidden
        <input type="password" name="pwd" hidden />　</p>

    <!--单选框标签
        input type="radio"
		name:表示组
        value:单选框的值
    -->
    <p>性别：这里用了禁用属性
        <input type="radio" name="sex" value="boy"  disabled/>男
        <input type="radio" name="sex" value="girl" />女
    </p>

    <!--多选框-->
    <p>爱好：
        <input type="checkbox" value="sleep" name="hobby"/>睡觉
        <input type="checkbox" value="code" name="hobby" checked/>敲代码
        <input type="checkbox" value="chat" name="hobby">聊天
    </p>

    <!--按钮
    input type="button"  普通按钮
    input type="image" 图像按钮
    input type="submit" 提交按钮
    input type="reset" 重置-->
    <p>按钮：
        <input type="button" value="点击变长" name="btn1"/>
        <!--<input type="image" src="../resource/img/1.png"/>-->
    </p>
```


```html
    <!--下拉框,列表框-->
    <p>
        <select name="列表名称">
            <option value="china">中国</option>
            <option value="us">美国</option>
            <option value="eth" selected>瑞士</option>
            <option value="india">印度</option>
        </select>
    </p>

    <!--文本域-->
    <p>反馈：
        <textarea name="textarea" cols="50" rows="10">文本内容</textarea>
    </p>

    <!--文件域-->
    <p>
        <input type="file" name="files"/>
        <input type="button" value="上传"　name="upload"/>
    </p>

    <!--邮件验证-->
    <p>邮箱：
        <input type="email" name="email"/>
    </p>

    <p>URL:
        <input type="url" name="url"/>
    </p>

    <!--数字验证-->
    <p>商品数量：
        <input type="number" name="num" max="100" min="0" step="1">
    </p>

    <!--滑块
        input type="range"
    -->
    <p>音量：
       <input type="range" name="voice" min="0" max="100" step="2"/>
    </p>

    <!--搜索框
    -->
    <p>搜索：
        <input type="search" name="search"/>
    </p>
    <p>
        <input type="submit">
        <input type="reset" value="清空表单"/>
    </p>
</form>
```

注意:

1.想chekbox和select都可以设置默认属性,直接在<>括号内设置checked和selected即可。

2.每个input中的name属性极为重要,因为它是判断数据传到后台的凭据。

textarea表示多行文本框,它的value值就是开始结束标签之间的内容。
