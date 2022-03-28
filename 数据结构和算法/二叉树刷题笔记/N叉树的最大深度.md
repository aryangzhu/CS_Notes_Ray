1.在我进行分析时，思路卡在了当前节点的具体应当干什么事儿这个步骤,看完大家的题解之后，

**树的最大深度=当前节点子节点的最大深度+1**

这个定理是破局的关键

```java
    void printIndent(int n) {
    	for (int i = 0; i < n; i++) {
        	System.out.printf(" ");
    	}
    }
```

这个方法可以提升我们的做题幸福感

类似的，可以自己编写打印方法

```java
  void printf(int n){
            System.out.printf("root.val=%d\n",n);
        }
```

注意，我们是count来控制缩进的距离，所以当打印一行时，可以这样来编写

```java
printIndent(count++);
//最后在返回时
count--;
```

为的是在同一函数中确保count不发生改变

不过不建议经常使用，当我们写代码的时候就应该考虑边界值，而不是测试出来

