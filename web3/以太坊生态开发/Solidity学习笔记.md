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

### 常数

`constant`（常量）和 `immutable`（不变量）。状态变量声明这两个关键字之后，不能在初始化后更改数值。这样做的好处是提升合约的安全性并节省 `gas。`

```solidity
uint256 constant CONSTANT_NUM = 10;//constant需要在声明的时候就初始化

address public immutable IMMUTABLE_ADDRESS;//不需要显示初始化，若是显式初始化的话即可声明初始化，也可以构造函数初始化，但是以constructor为准。

```

## 变量数据存储和作用域(重点)

### 数据位置

#### storage(重点)

存储在链上，消耗gas最多，也是合约变量中默认的存储位置

#### memory&calldata

memory:函数里的参数和临时变量一般用memory，存储在内存中，不上链。尤其是**如果返回数据类型是变长的情况下**，必须加memory修饰，例如：string，bytes，array和自定义结构。

calldata:和memory类似，存储在内存中，不上链。与memory的不同点在**于calldata变量不能修改(immutable)**,一般用户函数的参数。

```solidity
function fCalldata(uint[] calldata _x) public pure returns(uint[] calldata){
   return (_x);
}
```

### 数据位置和赋值规则

在不同存储类型相互赋值的时候，有时会产生独立的副本(修改新变量不会影响远变量)，有时会产生引用(修改新变量会影响原变量)，规则如下：

1. 将storage赋值给storage的时候，会创建引用，改变新变量会影响原变量。
   ```solidity
   uint[] x = [1,2,3]; // 状态变量：数组 x

   function fStorage() public{
       //声明一个storage的变量 xStorage，指向x。修改xStorage也会影响x
       uint[] storage xStorage = x;
       xStorage[0] = 100;
   }

   ```
2. memory赋值给memory的时候，也会创建引用，修改新变量会影响原变量。

其他情况下，比如将storage赋值给memory的时候，会创建独立副本，修改新变量不会影响原变量。

### 变量作用域

#### 状态变量

存储在链上的变量，所有合约函数都可以访问。这也解释了为什么默认存储位置是storage

```solidity
contract Variables {
  uint public x=1;
  uint public y;
  string public z;
}
```

#### 局部变量

函数执行过程中有效的变量，函数退出后，变量无效。不上链，gas低。

#### 全局变量

solidity预留的关键字，他们可以不声明而直接使用。

```solidity
function global() external view returns(address,uint,bytes memory){
   address sender=msg.sender;
   uint blockNum=block.number;
   byte memory data=msg.data;s
   return(sender,blockNum,datass);
}
```

msg.value 当前交易发送的wei值。

msg.sender 消息的发送者，也就是调用合约函数的地址。

msg.data 发起交易的完整calldata。

### 全局变量-以太单位和时间单位

1. 以太单位
   在基础篇上讲过，solidity中不存在小数点，以0代题为小数点。
   wei:1
2. 时间单位
   可以规定一个操作必须在一周内完成

```
seconds: 1
minutes: 60 seconds = 60
hours: 60 minutes = 3600
days: 24 hours = 86400
weeks: 7 days = 604800
```

## 变量初始化

### 值类型默认初始值

```solidity
bool public _bool; // false
string public _string; // ""
int public _int; // 0
uint public _uint; // 0
address public _address; // 0x0000000000000000000000000000000000000000

enum ActionSet { Buy, Hold, Sell}
ActionSet public _enum; // 第1个内容Buy的索引0

function fi() internal{} // internal空白函数
function fe() external{} // external空白函数 
span
```

### 引用类型默认初始值

```solidity
// Reference Types
uint[8] public _staticArray; // 所有成员设为其默认值的静态数组[0,0,0,0,0,0,0,0]
uint[] public _dynamicArray; // `[]`
mapping(uint => address) public _mapping; // 所有元素都为其默认值的mapping
// 所有成员设为其默认值的结构体 0, 0
struct Student{
    uint256 id;
    uint256 score; 
}
Student public student;

```

**delete操作符**

delete a会让变量a的值变为初始值

```solidity
boolean public _bool2=true;
function d() external{
   delete _bool2;
}
```

## 控制流

### 控制流

if-else

for循环

while循环

do-while循环

三元运算符

## 构造函数和修饰器

### 构造函数

```solidity
address owner;

constructor(address initialOwner){
   owner=initialOwner;
}
```

### 修饰器

对于方法添加判断或者增加一些额外功能

```solidity
modifier onlyOwner{
  require(msg.sender==owner);
  _;
}
```

代onlyOwner修饰符的函数只能被owner地址调用

```solidity
function changeOwner(address _newOwner) external onlyOwner{
   owner=_newOwner;
}
```

## 事件

### 事件

其实就是EVM日志的抽象，它有两个特点：

响应：应用程序(ethers.js)可以通过RPC接口订阅和监听这些事件，并在前端做响应。

经济：事件是EVM上比较经济的存储数据的方式，每个大概消耗2000 gas;相比之下，链上存储一个新变量至少需要20000 gas。

### 声明事件

事件的声明event关键字开头，接着是事件名称，括号里面写好事件需要记录的变量类型和变量名。

```solidity
event Transfer(address indexed from,address indexed to,uint256 value)
```

### 释放事件

可以在函数里面释放事件。

```solidity
function _transfer(
    address from,
    address to,
    uint256 amount
)external{
    _balances[from]=10000000;
    _balances[from]-=amount;
    _balances[to]+=amount;

    emit Transfer(from,to,amount);
}
```

### EVM日志

![1739985836113](image/Solidity学习笔记/1739985836113.png)

### 主题&数据&data

日志的第一部分是主题数组，用于描述事件，长度不能超过 `4`

除了事件哈希，主题还可以包含至多 `3`个 `indexed`参数，也就是 `Transfer`事件中的 `from`和 `to`。

data中存储了不带indexed的数据

## 继承

继承是面向对象编程很重要的组成部分，可以显著减少重复代码。

* `virtual`: 父合约中的函数，如果希望子合约重写，需要加上 `virtual`关键字。
* `override`：子合约重写了父合约中的函数，需要加上 `override`关键字。

### 简单继承&多重继承

多重继承就是一个子合约有多个父合约

### 修饰器的继承&构造函数的继承

修饰器和方法一样，这里构造函数继承比较特殊

```solidity
// 构造函数的继承
abstract contract A {
    uint public a;

    constructor(uint _a) {
        a = _a;
    }
}
```

ss

1. 在继承时声明父构造函数的参数，例如：`contract B is A(1)`
2. 在子合约的构造函数中声明构造函数的参数，例如：

```
   contract C is A {
       constructor(uint _c) A(_c * _c) {}
   }
```

### 函数的重载和重写

和Java中一样，也是有重载的概念的，即名字相同但是输入参数类型不同的函数可以同时存在(函数签名不同)

### 调用父合约的函数

1. 直接调用
   ```solidity
   function callParent() public{
       Yeye.pop();
   }
   ```
2. super关键字
   ```solidity
   function callParentSuper() public{
       // 将调用最近的父合约函数，Baba.pop()
       super.pop();
   }
   ```

### 钻石继承

```solidity
/* 继承树：
  God
 /  \
Adam Eve
 \  /
people
*/
```

## 抽象合约和接口

### 抽象合约

合约里至少有一个未实现的函数，即某个函数缺少主体{}中的内容，则合约必须用abstract修饰。

### 接口

对于一组功能的抽象，不实现任何功能。

1. 不能包含状态变量
2. 不能包含构造函数
3. 不能继承除接口外的其他合约
4. 所有函数都必须是external且不能有函数体
5. 继承接口的非抽象合约必须实现接口定义的所有功能

## 异常

### ERROR

```solidity
error TransferNotOwner();

function transferOwner1(uint256 tokenId,address newOwner) public{
   if(_owners[tokenId]!=msg.sender){
       revert TransferNotOwner();
   }
   _owners[tokenId]=newOwner;
}
```

### Require

gas随着描述异常的字符串长度增加

```solidity
function transferOwner2(uint256 tokenId,address newOwner){
   require(_owners[tokenId]==msg.sender,"Transfer Not Owner");
   _owners[tokenId]=newOwner;
}
```

### Assert

不能解释抛出异常的原因

```solidity
function transferOwner3(uint256 tokenId,address newOwner){
   assert(_owners[tokenId]==msg.sender);
   _owners[tokenId]=newOwner;
}
```

## 库合约

特殊合约，定义的用的library关键字，是一系列函数的合集，为了提升Solidity代码的复用性和减少gas费用。

1. 不能存在状态变量
2. 不能够继承或者被继承
3. 不能接收以太币
4. 不可以被销毁。

### 如何使用

using for

```solidity
// 利用using for指令
using Strings for uint256;
function getString1(uint256 _number) public pure returns(string memory){
    // 库合约中的函数会自动添加为uint256型变量的成员
    return _number.toHexString();
}
```

库合约名称调用函数

```solidity
// 直接通过库合约名调用
function getString2(uint256 _number) public pure returns(string memory){
    return Strings.toHexString(_number);
}
```

## import

其实我感觉有import就可以了，不知道为啥还需要上面库合约，import其实就是在一个文件中引用另一个文件的内容。

### import用法

```solidity
import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol';

```

## 接收ETH和发送ETH

### 接收ETH

#### 接收ETH函数receive

```solidity
// 定义事件
event Received(address Sender, uint Value);
// 接收ETH时释放Received事件
receive() external payable {
    emit Received(msg.sender, msg.value);
}
```

#### 回退函数fallback

会在调用合约不存在的函数时被处罚。可用于接收ETH,也可以用于代理合约proxy contract。

```solidity
event fallbackCalled(address Sender,uint Value,bytes Data);

fallback() external payable{
    emit fallbackCalled(msg.sender,msg.value,msg.data);
}
```

#### receive和fallback的区别

```
触发fallback() 还是 receive()?
           接收ETH
              |
         msg.data是空？
            /  \
          是    否
          /      \
receive()存在?   fallback()
        / \
       是  否
      /     \
receive()   fallback()

```

### 发送ETH

1. transfer
   用法是接收发地址.transfer(发送ETH数额)，限制是2300，足够用于转帐，但对方合约的fallback()或receive()函数不能实现太复杂的逻辑，转帐失败会自动revert

   ```solidity
   // 用transfer()发送ETH
   function transferETH(address payable _to, uint256 amount) external payable{
       _to.transfer(amount);
   }
   ```
2. send

   用法是接收方地址.send(发送ETH数额)，send的限制也是2300，和上面面一样，足够用于转账，但对方合约的fallback()或者receive()函数不能实现太复杂的逻辑，send()如果转账失败，不会revert，send()的返回值是bool，代表着转账成功或者失败，需要额外代码处理一下。

   ```solidity
   error SendFailed();

   function sendETH(address payable _to,uint256 amount) external payable{
      bool success=_to.send(amount);
      if(!success){
          revert SendFailed();
      }
   }
   ```
3. call
   用法是接收方地址.call{value:发送ETH数额}("")。
   call没有gas费用限制，需要手动转账失败情况。

## 调用其他合约

### 通过合约地址调用

```solidity
function callSetX(address _Address,uint256 x) external{
    OtherContract(_Address).setX(x);
}
```

### 传入合约变量调用

```solidity
function callGetX(OtherContract _Address) external view returns(uint x){
   x=_Address.getX();
}
```

### 将地址参数创建为合约变量调用

```solidity
function callGetX2(address _Address) external view returns(uint x){
    OtherContract oc=OtherContract(_Address);
    x=oc.getX();
}
```

### 调用合约并发送

```solidity
function setXTransferETH(address otherContract,uint256 x) payable external{
   OtheContract(otherContract).setX{value:msg.value}(x);
}
```

## call&Delegatecall

call不仅可以转账，还可以用来调用其他合约函数

### call的使用规则

目标合约地址.call(字节码);

abi.encodeWithSignature("函数签名",逗号分隔的具体参数);

另外call在调用合约时可以指定交易发送的ETH数额和gas数额

目标合约地址.call{value:发送数额,gas:gas数额}(字节码);

我们直接通过代码来看吧

```solidity
function callSetX(address payable _addr,uint256 x) public payable{
    (bool success,bytes memory data)=_addr.call{value:msg.value}(
        abi.encodeWithSignature("seteX(uint256)",x);
    );
}
```

### Delegatecall使用规则

solidity中大型项目为了将逻辑结构和存储结构隔离开，就出现了代理合约。

delegatecall其实就是委托代理调用，用来增强方法???

代码更加直观

```solidity
funciton delegatecallSetVars(address _addr,uint _num){
  (bool success,byte memory data)_addr.delegatecall(
     abi.encodeWithSignature("setVars(uint256)",num);
  );
}
```

假如说有A,B,C3个合约，正常A->B->C的话，那么c的被调用函数的上下文就是B的，但是如果走了代理那么就是A

## create&create2

去中心化交易所uniswap利用工厂合约(PairFactory)创建了无数的币对合约

### create

就是通过new一个新合约，并传入新合约构造函数所需的参数

```solidity
Contract x=new Contract{Value:_value}(params);
```

### 极简Uniswap

有两个核心的合约，UniswapV2Pair:币对合约，用于管理币对地址、流动性和买卖;UninswapV2Factory:工厂合约，用于创建新的币对，并管理币对地址。

这里代码就不放出来了，不然纯抄了哈哈哈

### create2

新地址 = hash(创建者地址, nonce)，这是传统的地址的计算方式，nonce是递增的，导致每次生成的都不一样

#### create2如何计算地址

1. 0xFF:一个常数，避免和CREATE冲突
2. CreatorAddress:调用CREATE2的当前合约(创建)地址。
3. salt(盐):一个创建者指定的bytes32类型的值，它的主要目的是用来影响新创建的合约的地址。
4. initcode:新合约的初始字节码(合约的Creation Code和构造函数的参数)
   其实就是预先计算出合约的地址，有些时候我们需要升级或替换现有的合约，使用CREATE2可以保证地址部署在相同的地址上。

## 删除合约

selfdestruct(_addr);
将合约剩余数额转至调用者。

## ABI编码和解码

ABI (Application Binary Interface，应用二进制接口)是与以太坊智能合约交互的标准，给前端的也是这个东西。

### 常用函数

abi.encode、abi.encodePacked、abi.encodeWithSignature和abi.encodeWithSelector

### 使用场景

1. 在合约中，配合call来实现对合约的底层调用

```solidity
bytes4 selector = contract.getValue.selector;

bytes memory data = abi.encodeWithSelector(selector, _x);
(bool success, bytes memory returnedData) = address(contract).staticcall(data);
require(success);

return abi.decode(returnedData, (uint256));
```

2. ethers.js常用ABI来实现合约的导入和函数调用

```javascript
const wavePortalContract = new ethers.Contract(contractAddress, contractABI, signer);
/*
* Call the getAllWaves method from your Smart Contract
*/
const waves = await wavePortalContract.getAllWaves();

```

## 哈希

### Keccak256

`Keccak256`函数是 `Solidity`中最常用的哈希函数，用法非常简单：

```
哈希 = keccak256(数据);
```

## 选择器

### 如何使用

```solidity
// 使用selector来调用函数
function callWithSignature() external{
	//...
   // 调用elementaryParamSelector函数
   (bool success1, bytes memory data1) = address(this).call(abi.encodeWithSelector(0x3ec37834, 1, 0));
//...
}
```

### methodId和参数

计算一个方法的methodId

```solidity
function mintSelector() external pure returns(bytes4 mSelector){
    return bytes4(keccak256("mint(address)"));
}
```

## Try Catch

只能用于external函数或者public函数或者创建合约时constructor

```solidity
function execute(uint amount) external returns(bool success){
   try even.onlyEven(amount) returns(bool _success){
       emit SuccessEvent();
       return _success;
   }catch Error(string memory reason){
       emit CatchEvent(reasonßß);
   }
}
```
