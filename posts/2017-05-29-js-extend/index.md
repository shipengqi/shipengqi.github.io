# Javascript 继承


Javascript 实现继承的 5 种方式。

<!--more-->

例如实现 `Student` 对 `Man` 的继承：

```javascript
function Man(){
    this.sex = "男";
}

function Student(name,age){
    this.name = name;
    this.age = age;
}
```

## 使用 call 或 apply 方法

```javascript
function Student(name,age){
    Man.apply(this, arguments);//  或者 Man.call(this)
    this.name = name;
    this.age = age;
}
var s = new Student("xiaoming", 18);
console.log(s.sex); // 男
```

## 原型链方式

```javascript
Student.prototype = new Man();
Student.prototype.constructor = Student; // 因为上一行代码使 Student.prototype.constructor 指向了 Man 的构造函数，
                                         // 但是 prototype 的 constructor 属性都应该指向它的构造函数，
                                         // 所以这里手动让 Student.prototype.constructor 指向 Student
var s = new Student("xiaoming", 18);
console.log(s.sex); // 男
```

## 直接继承 prototype

对原型链方式的改进。与原型链方式相比，这种方式省去了 new 一个 Man 实例的步骤，节省内存。但是 `Student.prototype` 和 `Man.prototype` 指向
了同一个对象，那么对 `Student.prototype` 的修改，也会反映到 `Man.prototype`，这不是我们想看到的。

```javascript
Student.prototype = Man.prototype;
Student.prototype.constructor = Student;
var s = new Student("xiaoming", 18);
console.log(s.sex); // 男
```

## 利用空对象实现继承

```javascript
var F = function(){};
F.prototype = Man.prototype;
Student.prototype = new F();
Student.prototype.constructor = Student;
```

F是空对象，几乎不占内存。而且修改 `Student.prototype`，不会影响到 `Man.prototype`。

## 拷贝继承

```javascript
function extend(Child, Parent) {
    var p = Parent.prototype;
    var c = Child.prototype;
    for (var i in p) {
        c[i] = p[i];
    }
}
extend(Student, Man);
var s = new Student("xiaoming", 18);
console.log(s.sex); // 男

```

效率较低，内存占用高（因为要拷贝父类的属性），而且无法获取父类不可枚举的方法（不可枚举方法，不能使用 `for in` 访问到）

## 多重继承

```javascript
function Class1()
{
  this.showSub = function(a,b)
  {
    console.log(a-b);
  }
}

function Class2()
{
  this.showAdd = function(a,b)
  {
    console.log(a+b);
  }
}

function Class3()
{
  Class1.call(this);
  Class2.call(this);
}
```

## Node.js 常用的实现继承的方法

```javascript
var util = require('util');

function Man(){
    this.sex = "男";
}

function Student(name,age){
    Man.call(this); // 继承属性
    this.name = name;
    this.age = age;
}

util.inherits(Student, Man);// 继承方法
```

