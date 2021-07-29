# 第八章 对象、类与面向对象编程
本章内容为对象、对象创建过程、继承、类
## 8.1 理解对象
可以使用new Object()或者字面量方式创建对象。
### 8.1.1 属性的类型
数据属性和访问器属性两种。
#### 1.数据属性
数据属性包含一个保存数据值的位置，值会从这个位置读取，也会写到这个位置。数据属性有四个特性：
* [[Configureable]]：属性是否可以通过delete删除并重新定义，修改它的特性，修改为访问器属性。默认为true。设为false后无法再设为true。
* [[Enumerable]]：属性是否可以通过for-in循环返回，默认true
* [[Writable]]：属性是否可以被修改，默认true
* [[Configureable]]：包含属性实际的值，即读取写入的位置，默认undefined

若要修改属性的默认特性，必须使用Object.defineProperty()。该方法接收三个参数：目标对象、属性名称、描述符对象。描述符对象的属性可以包含若干个上述四个特性，一一对应关系。前三项值在调用该方法且未指定时，都默认为false。
#### 2.访问器属性
访问器属性不包含数据值。它们包含一个获取函数、设置函数，不过这两个函数不是必须的。在读取访问器属性时，会调用获取函数，这个函数的责任就是返回一个有效的值。在写入访问器属性时，会调用设置函数并传入新值，这个函数必须决定对数据做出什么修改。访问器属性有4个特性：
* [[Configureable]]：属性是否可以通过delete删除并重新定义，修改它的特性，修改为访问器属性。默认为true。设为false后无法再设为true。
* [[Enumerable]]：属性是否可以通过for-in循环返回，默认true
* [[Get]]：获取函数，在读取属性时调用，默认值为undefined
* [[Set]]：设置函数，在写入属性时调用，默认值为undefined

访问器属性必须使用Object.defineProperty()定义。访问器属性的典型使用场景是设置一个属性值会导致一些其他的变化。

数据属性与访问器属性的相互转换https://www.cnblogs.com/shiningly/p/9482283.html



### 8.1.2 定义多个属性
Object.defineProperties()可以通过多个描述符一次性定义多个属性。它接收两个参数：要为之添加或修改属性的对象和另一个描述符对象，其属性与要添加或修改的属性一一对应。
### 8.1.3 读取属性的特性
Object.getOwnPropertyDescriptor()方法可以取得指定属性的属性描述符。该方法接收两个参数：属性所在的对象和要取得其描述符的属性名。返回值是一个对象包含相应的四个属性。

还有Object.getOwnPeopertyDescriptors()静态方法，这个方法会在每个自有属性调用上个方法，并返回对象。接收一个对象参数。
### 8.1.4 合并对象
混入方法Object.assign()，这个方法接受一个目标对象和若干个源对象作为参数，然后将每个源对象中可枚举(Object.propertyIsEmuerable()返回true)和自有(Object.hasOwnProperty()返回true)属性复制到目标对象。以字符串和符号为键的属性会被复制。对每个符合条件的属性，这个方法会使用源对象上的[[Get]]取得属性的值，然后使用目标对象上的[[Set]]设置属性的值。

Object.assign()实际上对每个源对象执行的是浅复制。如果多个源对象有相同的属性，则使用最后一个复制的值。此外，从源对象访问器属性取得的值，比如获取函数，会作为一个静态值赋给目标对象。即不能在两个对象间转移获取函数和设置函数。
### 8.1.5 对象标识及相等判定
可以使用Object.is()代替 === ，这个方法必须接收两个参数。

    要检查超过两个值，递归地利用相等性传递即可：
    function recursivelyCheckEqual(x, ...rest) { 
      return Object.is(x, rest[0]) && 
      (rest.length < 2 || recursivelyCheckEqual(...rest)); 
    }
### 8.1.6 增强的对象语法
#### 1.属性值简写
给对象添加变量时，若属性名和变量名是一样的，可简写只写变量名。代码压缩会在不同作用域间保留属性名，以防止找不到引用。
#### 2.可计算属性
可以在对象字面量中进行动态属性赋值。中括号包围的对象属性键表示将其作为表达式而不是字符串来求值。
#### 3.简写方法名
简写方法名对获取函数和设置函数也是适用的。简写方法名与可计算属性键相互兼容。
### 8.1.7 对象解构
可以在一条语句中使用嵌套数据实现一个或多个赋值操作。简单地说就是使用与对象相匹配的结构来实现对象属性赋值。如果引用的属性不存在，则该变量的值就是undefined。解构在内部使用函数ToObject()把源数据结构转换为对象。

    let { name: personName, age: personAge } = person;
    let { name, age } = person; //可以使用简写语法。
    let { name, job='Software engineer' } = person; //可以定义默认值
    let { constructor: c } = 4; // 原始值会被当做对象。所以null、undefined不能被解构。
    console.log(c === Number); // true
解构并不要求变量必须在解构表达式中声明。不过，如果是给事先声明的变量赋值，则赋值表达式
必须包含在一对括号中。

    let personName, personAge; 
    let person = { 
     name: 'Matt', 
     age: 27 
    }; 
    ({name: personName, age: personAge} = person);
#### 1.嵌套解构
解构对于引进嵌套属性或赋值目标没有限制。解构赋值可以使用嵌套解构，以匹配嵌套属性。在外层属性没有定义的情况下不能使用嵌套解构。无论是源代码还是目标对象。
#### 2.部分解构
涉及多个属性的解构是一个输出无关的顺序化操作。如果一个解构表达式涉及多个赋值，且其中的赋值会报错，则出错之后的解构赋值会失败。
#### 3.参数上下文匹配
在函数参数列表中也可以进行解构赋值。
## 8.2 创建对象
若创建多个具有相同接口的对象，使用Object构造函数或对象字面量都有明显不足。
### 8.2.1 概述
ECMAScript 6 支持类和继承，但仅仅是封装了ES 5.1构造函数加原型继承的语法糖而已。
### 8.2.2 工厂模式
    function createPerson(name, age, job) { 
     let o = new Object(); 
     o.name = name; 
     o.age = age; 
     o.job = job; 
     o.sayName = function() { 
     console.log(this.name); 
     }; 
     return o; 
    } 
    let person1 = createPerson("Nicholas", 29, "Software Engineer"); 
    let person2 = createPerson("Greg", 27, "Doctor");
工厂模式虽然解决了创建多个类似对象的问题，但没有解决对象标识问题(即新创建的对象是什么类型)。









