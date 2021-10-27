表单验证
	表单验证我们经常会用到，所以有必要进行一下学习

    ````html
    <!--placeholder 提示信息
                    required　非空判定
                    pattern 正则表达式-->
     <input type="text" name="username" placeholder="请输入用户名" required/>
    ````

```html
     <p>自定义邮箱：
            <input type="text" name="diymail" pattern="/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/
	/^[a-z\d]+(\.[a-z\d]+)*@([\da-z](-[\da-z])?)+(\.{1,2}[a-z]+)+$/或\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+	([-.]\w+)*"/>这里直接去网上搜
     </p>
```



