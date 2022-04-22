参考文章狂神boot系列

# 创建一个springboot项目

选择SpringBoot Intlizer然后选择Java Web组件

# yml文件

目的是作为配置文件来使用

## 配置文件

作用:修改SpringBoot自动配置的默认值,因为SpringBoot在底层给我们配置好了。

例如

```
server.port=8081;
```

## yml描述

让我们先回顾一下xml长什么样子

```xml
<server>
    <port>8081<port>
</server>
```

**yml以数据作为中心,而不是以标记语言为重点**

yml配置

```yml
server：
  prot: 8080
```

## yml基础语法

### 注意事项

1.空格不能省略。

2.以缩进来控制层级关系,只要是左边对齐的一列数据都是同一个层级的。

3.属性和值的大小都是十分敏感的。

### 字面量[数字,布尔值,字符串]

```
k: v
```

#### 注意

“”不会转义字符串里面的特殊字符,特殊字符会作为本身想表示的意思

比如:name:"kuang \n shen"，输出:kuang换行shen

''单引号会转义特殊字符

### 对象、Map(键值对)

```yml
#对象、Map格式
k:
	v1:
	v2:
```

缩进写法

```yml
student:
	name:qinjiang
	age:3
```

行内写法

```yml
student:{name:qinjiang,age:3}
```

### 数组

用-值表示数组中的一个元素,比如:

```yml
pets:
	- cat
	- dog
	- pig
```

行内写法

```yml
pets:[cat,dog,pig]
```

### 注入配置文件

