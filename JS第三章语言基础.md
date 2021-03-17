# JS高级第三章语言基础
## 语法
### 区分大小写
&emsp;&emsp;ECMAScript中一切都区分大小写：变量、函数名和操作符。
### 标识符
&emsp;&emsp;标识符，就是变量、函数、属性或函数参数的名称。标识符的组成：
* 第一个字符必须是一个字母、下划线或美元符号。
* 其他符号可以是上述加数字。
* 标识符可以使用Unicode的字母符号，但不建议。
&emsp;&emsp;驼峰命名法也是ECMAScript内置函数命名方式
### 注释
    // 单行注释

    /* 这是多行
    注释 */
### 严格模式
&emsp;&emsp;"use strict";

&emsp;&emsp;可以写在函数体中，来单独指定一个函数。
### 语句
&emsp;&emsp;语句结尾加分号。

&emsp;&emsp;代码块。
## 关键字与保留字
&emsp;&emsp;虽然这些词汇可以当做对象的属性名，但不建议
### 所有关键字
|||||
| :----: | :----: | :----: | :----: |
|break|do|in|typeof|
|case|else|instanceof|var|
|catch|export|new|void|
|class|extends|return|while|
|const|finally|super|with|
|continue|for|switch|yield|
|debugger|function|this|
|default|if|throw|
|delete|import|try|
### 未来的保留字
&emsp;&emsp;enum
### 严格模式下保留
||||
| :----: | :----: | :----: |
|implements|package|public|
|interface|protected|static|
|let|private|
### 代码模块中保留
&emsp;&emsp;await
## 变量
&emsp;&emsp;JS中变量是松散类型的，可以保存任意类型的数据。变量是保存任意值的命名占位符。

&emsp;&emsp;var、const、let
### var关键字
&emsp;&emsp;不初始化的情况下会保存一个undefined。不推荐改变变量保存值的类型
#### var声明作用域
&emsp;&emsp;使用var操作符定义的变量会成为包含他的函数的局部变量。若去掉var操作符，可以创建一个全局变量。
#### var声明提升
&emsp;&emsp;var生命的变量会自动提升到函数作用域顶部，但是赋值不会提升。
### let声明
&emsp;&emsp;let声明的是块作用域，var声明的是函数作用域。块作用域是函数作用域的子集。let也不允许同一个作用于出现冗余声明，包括let和var各一次。
#### 暂时性死区
&emsp;&emsp;let声明的变量不会在作用域中被提升。
#### 全局声明
&emsp;&emsp;使用let在全局作用域中声明的变量不会成为window对象的属性。
#### 条件声明
&emsp;&emsp;let不能依赖条件模式声明。
#### for循环中的let声明
&emsp;&emsp;let定义的for循环迭代变量不会渗透到循环体外部。并且JS引擎会为每一个迭代循环声明一个新的迭代变量。而var声明的变量在推出循环时，迭代变量储存的是导致循环退出的值，始终是一个值。
### const声明
&emsp;&emsp;与let类似，const不允许重复声明、作用域是块。但是const在声明时必须同时初始化变量，且无法再修改变量的值。但const生命的限制只适用于它指向的变量的引用，所以可以修改它指向的变量的属性。

&emsp;&emsp;const不可以声明i++之类的自增的迭代变量，但可以用于不会被修改的循环变量，用于for-of和for-in。
### 声明风格及最佳实践
#### 不适用var
#### const优先，let次之
&emsp;&emsp;这样可以













