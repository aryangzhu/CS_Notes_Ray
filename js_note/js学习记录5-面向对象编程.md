1.和Java等面向对象不太一样
JS链式继承
原型
```
 var Student={
            name:"leiliu",
            age:3,
            run :function () {
                console.log(this.name+"run....");
            }
        };

        var xiaoming={
            name:"xiaoming"
        };

        //原型对象
        xiaoming.__proto__=Student;
```
**class**关键字，是在ES6中引入的
1.定义一个类，属性，方法
```
  //js定义类下的属性和方法
        function Student(name) {
            this.name = name;
        }

        Student.prototype.hello=function () {
            alert("hello");
        }

        //es6引入的
        class Student{
            constructor(name){
                this.name=name;
            }

            hello(){
                alert('hello')
            }
        }

        var xiaoming=new Student("xiaoming");
```
**原型链**
__proto__: