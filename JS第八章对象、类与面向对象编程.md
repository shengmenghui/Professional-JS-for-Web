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

数据属性与访问器属性的相互转换<https://www.cnblogs.com/shiningly/p/9482283.html>

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

### 8.2.3 构造函数模式

构造函数是用于创建特定类型对象的。Object和Array是原生构造函数。可以以函数的形式自定义构造函数来定义属性和方法。

        function Person(name, age, job){ 
          this.name = name; 
          this.age = age; 
          this.job = job; 
          this.sayName = function() { 
            console.log(this.name); 
          }; 
        } 
        let person1 = new Person("Nicholas", 29, "Software Engineer"); 
        let person2 = new Person("Greg", 27, "Doctor"); 
        person1.sayName(); // Nicholas 
        person2.sayName(); // Greg
和上面的区别是

* 没有显示的创建对象
* 属性和方法直接赋值给了this
* 没有return

按惯例，构造函数名称首字母都是要大写的，使用构造函数要使用new操作符。会执行如下操作：

* 在内存中传建一个对象。
* 这个新对象内部的[[Prototype]]特性被赋值为构造函数的prototype属性。
* 构造函数内部的this被赋值为这个新对象(即this指向新对象)。
* 执行构造函数内部的代码(给新对象添加属性)
* 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象

上面例子最后的两个对象都有一个constructor属性指向Person。instanceof操作符也可以确定对象类型。

构造函数不一定要写成函数声明的形式。赋值给变量的函数表达式也可以表示构造函数：

    let Person = function(name, age, job) { 
       this.name = name; 
       this.age = age; 
       this.job = job; 
       this.sayName = function() { 
         console.log(this.name); 
       }; 
    }
如果不想传参的话，函数后面的括号可以省略。

#### 1.构造函数也是函数

构造函数与普通函数唯一的区别是调用方式不同。构造函数也是函数，并没有把某个函数定义为构造函数的特殊用法。任何函数只要使用new操作符低啊用的就是构造函数，而不使用new操作符调用的函数就是普通函数。

    // 作为构造函数
    let person = new Person("Nicholas", 29, "Software Engineer"); 
    person.sayName(); // "Nicholas" 
    // 作为函数调用
    Person("Greg", 27, "Doctor"); // 添加到 window 对象
    window.sayName(); // "Greg" 
    // 在另一个对象的作用域中调用
    let o = new Object(); 
    Person.call(o, "Kristen", 25, "Nurse"); 
    o.sayName(); // "Kristen"
在调用一个函数而没有明确设置this值的情况下(即没有作为对象的方法调用，或者没有使用call()/apply()调用)，this就会指向全局对象(在浏览器中就是window对象)。

#### 2.构造函数的问题

构造函数定义的方法会在每个实例上都创建一遍。因此不同实例上的函数不相等。解决方案是把函数定义转移到构造函数外部。

    function Person(name, age, job){ 
      this.name = name; 
      this.age = age; 
      this.job = job; 
      this.sayName = sayName; 
    } 
    function sayName() { 
      console.log(this.name); 
    }
但是这样会导致函数被定义在全局作用域上，导致自定义类型引用的代码不能很好地聚集在一起。

### 8.2.4 原型模式

每个函数都会创建一个 prototype 属性，这个属性是一个对象，包含应该由特定引用类型的实例共享的属性和方法。实际上，这个对象就是通过调用构造函数创建的对象的原型。使用原型对象的好处是，在它上面定义的属性和方法可以被对象实例共享。原来在构造函数中直接赋给对象实例的值，可以直接赋值给它们的原型，如下所示：

    function Person() {} // 函数表达式写法：let Person = function() {};

    Person.prototype.name = "Nicholas"; 
    Person.prototype.age = 29; 
    Person.prototype.job = "Software Engineer"; 
    Person.prototype.sayName = function() { 
      console.log(this.name); 
    }; 
    let person1 = new Person(); 
    person1.sayName(); // "Nicholas" 
    let person2 = new Person(); 
    person2.sayName(); // "Nicholas" 
    console.log(person1.sayName == person2.sayName); // true

#### 1.理解原型

创建一个函数就会为这个函数创建一个prototype属性(指向原型对象)。所有原型对象自动获得一个名为constructor的属性，指回与之关联的构造函数。如Person.prototype.constructor 指向 Person。

在自定义构造函数时，原型对象默认只会获得constructor属性，其他的所有方法都继承自Object。每次调用一个构造函数创建一个实例，这个实例的内部[[Prototype]]指针就会被赋值为构造函数的原型对象，可以通过暴露的__proto__属性来访问对象的原型。

实例与构造函数原型之间有直接的联系，但实例与构造函数之间没有。

构造函数可以是函数表达式也可以是函数声明。声明之后构造函数就有了一个与之关联的原型对象。正常的原型链都会止于Object的原型对象null。构造函数、原型对象和实例是3个完全不同的对象。实例通过__proto__链接到原型对象，它实际指向隐藏特性[[Prototype]]。构造函数通过prototype属性链接到原型对象。实例与构造函数没有直接关系，与原型对象有关系。同一个构造函数创建的两个实例共享一个原型对象。instanceof检查实例的原型链中是否包含指定构造函数的原型。

原型属性查找机制。

因为不是所有实现都对外暴露了[[Prototype]]，但可以使用isPrototypeOf()方法查找两个对象之间的关系。该方法会在传入参数的[[Prototype]]指向调用它的对象时返回true。

Object类型有一个方法叫Object.getPrototypeOf()，返回参数内部属性[[Prototype]]的值。该方法可以较方便取得一个对象的原型，这在通过原型实现继承时很重要。

Object.setPrototypeOf()可以向实例的私有特性[[Prototype]]写入新值，即可重写一个对象的原型继承关系。不过该方法可能会严重影响性能。所以推荐使用Object.create()老创建一个新对象，同时为其指定原型。

#### 2.原型层级

通过对象访问属性时，会按照属性名称开始搜索。先搜索对象实例本身，没有再向上搜索原型对象。constructor属性只存在于原型对象，因此通过实例对象也可以访问。只要给对象实例添加一个属性，这个属性就会**遮蔽**原型对象上的同名属性，不会修改但会屏蔽访问。只能通过delete操作符完全删除实例上的属性，才能让标识符解析过程能够继续搜索原型对象。

hasOwnProperty()方法会返回一个布尔值，指示对象自身属性中是否具有指定的属性（也就是，是否有指定的键）。

#### 3.原型和in操作符

##### 单独使用

如果指定的属性在指定的对象或其原型链中，则in运算符返回true。如果要确定某个属性是否存在于原型上，可以用!object.hasOwnProperty(name) && (name in object)。

##### for-in循环

以任意顺序遍历一个对象的除Symbol以外的可枚举属性。可以通过对象访问且可以被枚举的属性都会返回，包括实例属性和原型属性。**遮蔽原型中不可枚举（[[Enumerable]]特性被设置为false）属性的实例属性也会在for-in循环中返回，因为默认情况下开发者定义的属性都是可枚举的。**？？？？

Object.keys()方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和正常循环遍历该对象时返回的顺序一致 。

Object.getOwnPropertyNames()方法返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性但不包括Symbol值作为名称的属性）组成的数组。

    let keys = Object.getOwnPropertyNames(Person.prototype); 
    console.log(keys); // "[constructor,name,age,job,sayName]" 包括不可枚举属性constructor。

Object.getOwnPropertySymbols() 方法返回一个给定对象自身的所有 Symbol 属性的数组。

#### 4.属性枚举顺序

for-in循环和Object.keys()的枚举顺序是不确定的，取决于JS引擎，因浏览器而异。Object.getOwnPropertyNames()、bject.getOwnPropertySymbols()和 Object.assign()的枚举顺序是确定性的：先以升序枚举数值键，然后以插入顺序枚举字符串和符号键。在对象字面量中定义的键以它们逗号分隔的顺序插入。

### 8.2.5 对象迭代

Object.values()方法返回一个给定对象自身的所有可枚举属性值的数组，Object.entries()方法返回一个给定对象自身可枚举属性的键值对数组。值的顺序与使用for...in循环的顺序相同(区别在于 for-in 循环枚举原型链中的属性)。非字符串属性会被转换为字符串输出。这两个方法执行对象的浅复制。符号属性会被忽略。

#### 1.其他原型对象

我们通常直接通过一个包含所有属性和方法的对象字面量来重写原型，这会导致constructor属性不指向Person。若我们创建原型对象时也通过字面量给constructor属性赋值为构造函数，虽然能达到重新定向的效果，但[[Enumerable]]会默认为true。所以我们需要使用Object.defineProperty()方法来定义constructor属性。

#### 2.原型动态性

任何时候对原型对象所做的修改，也会在实例上反映出来。

重写整个原型会切断最初原型与构造函数的联系，但实例引用的仍是最初的原型。实例只有指向原型的指针，没有指向构造函数的指针。重写构造函数上的原型之后再创建的实例才会引用新的原型。而在此之前创建的实例仍然会引用最
初的原型。

#### 3.原生对象原型

原声引用类型模式即是原型模式，是通过原生引用类型的构造函数在原型定义实例方法来实现的。原生对象原型也可以进行修改，但不建议这么做。

#### 4.原型的问题

* 弱化向构造函数传递初始化参数的能力
* 共享特性的引用属性容易混淆

## 继承

接口继承只继承方法签名，在es中是不可能的。只有集成方法的实现继承可以通过原型链实现。

### 8.3.1 原型链

基本思想是通过原型继承多个引用类型的属性和方法。
