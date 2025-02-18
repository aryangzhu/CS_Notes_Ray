### 写在前面

本篇笔记参考的教程是WTF学院的101和102的lab，官方的文档我也有看过但是中文版读起来很奇怪，所以就选择了这个教程。我跟学下来觉得很赞，每一小节都有测验来检查我们吸收的怎么样，强烈推荐。103的lab我看了一下都是各个方向的实际应用讲解，我后面单读写吧，因为我觉得每个方向都还是需要一些基础概念做打底的。

## 认识合约

合约就是在区块链基础上运行的程序(数据+操作)，区块链保存交易信息，合约中的操作就相当于交易需要消耗gas，同时区块链中还会保存合约中的状态变量，所以读取合约的状态变量也是交易，也需要消耗gas费用。

## 数据类型

和其他语言入门一样，Solidity也是从数据类型开始讲起，我学习前端语言(js，ts)和其他后端语言(python)也是如此。

### 值类型

#### 整型

常见的写法

```solidity
int public _int=-1; //整数，包括负数
uint public _uint =1; //无符号整数
uint256 public _number =20220330; //256位无符号整数
```

#### 布尔类型

```solidity
bool public _bool1=true;
```

##### 逻辑运算

！、&&、||、==、!=

#### 地址类型

这个算是solidity里面比较特殊的一个了，我们知道合约都是EOA和CA。在合约中我们也需要用到地址，所以就有了这种类型。

```solidity
address public _address=0x7A58c0Be72BE218B41C608b7Fe7C5bB630736C71;
address payable poublic _address1= payable(_address);
uint256 public balance=address1.balance;
```

##### 普通地址

存储一个20字节的值(以太坊地址)的大小。

##### payable address

比普通地址多了transfer和send(两个都是用于转账的方法)，后面会提到

#### 定长字节数组

##### 定长字节数组

声明以后不能改变

##### 不定长字节数组

属于引用类型，数组长度可更改

```
// 固定长度的字节数组
bytes32 public _byte32 = "MiniSolidity";
```

注：byte_其实就是byte32的第一个字节了。

#### 枚举

```solidity
// 用enum将uint 0， 1， 2表示为Buy, Hold, Sell
enum ActionSet { Buy, Hold, Sell }
// 创建enum变量 action
ActionSet action = ActionSet.Buy;
```

### 函数类型(重点)

```solidity
function <function name>([parameter types[, ...]]) {internal|external|public|private} [pure|view|payable] [virtual|override] [<modifiers>]
[returns (<return types>)]{ <function body> }

```

1. 函数名
2. parameter types[,....]参数
3. {internalexternal|public|private}:可见性说明符
   public:内外部均可见
   private:只能从合约内部访问，继承的也不能用
   external:只能从外部访问(但是内部可以通过this.f()来调用)
   internal:只能从合约内部访问，继承的合约可以用
4. [pure|view|payable] 决定函数权限/功能的关键字
   被pure修饰的方法里面不能读合约状态变量，也不写
   view修饰可读不可写
   payalbe就是调用的时候可以转账(默认是往往合约里面转账)，这里教程说的太简单了，其实意思是函数在调用时可以接收函数，我猜测是先转到合约账户里，如果有需要的话转到指定账户地址(方法体中实现)
5. [virtual|override] 方法是否可以被重写
6. `<modifiers>` 函数修饰器，相当于装饰器模式的在语言中直接体现了
7. `<function body> 函数体。`

#### 函数输出

```solidity
// 返回多个变量
function returnMultiple() public pure returns(uint256, bool, uint256[3] memory){
    return(1, true, [uint256(1),2,5]);
}
```

returns定义了函数应该返回什么样的形式，而return返回了实际的值。

### 引用类型

#### 数组Array

用来存储一组数据(整数、字节，地址等等)

##### 固定长度数组

```solidity
// 固定长度 Array
uint[8] array1;
bytes1[5] array2;
address[100] array3;
```

##### 可变长度数组

```solidity
// 可变长度 Array
uint[] array4;
bytes1[] array5;
address[] array6;
bytes array7;
//bytes比较特殊，是数组，但是不用加[]，单字节要用bytes或者bytes1
```

##### 数组常用成员

length、push()、push(x)和pop()

#### 结构体struct

通过结构体的形式定义新的类型。

```solidity
struct Student{
  uint256 id;
  uint256 score;
}
```

### 映射类型

非常常见，就是哈希表。

#### 映射Mapping

声明映射的格式为 `mapping(_KeyType => _ValueType)`

```solidity
mapping(uint => address) public idToAddress; // id映射到地址
mapping(address => address) public swapPair; // 币对的映射，地址到地址
```

#### 映射的规则

1. _KeyType只能选择Solidity内置的值类型，比如uint，address等。
2. 映射的存储位置必须是storage,因此用于合约的状态变量(这个存储位置后面我们会提到)
3. 如果映射声明位public，那么Solidity会自动给你创建一个getter函数，可以通过Key来查询对应的Value。
4. 给新映射的键值对赋值的语法位_Var[\_Key]=_Value;

## 变量初始化(重点)

## 常数

## 控制流

## 构造函数和修饰器

## 事件

## 继承

## 抽象合约和接口

## 异常
