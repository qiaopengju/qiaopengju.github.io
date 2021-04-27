---
title: Dart
hide: false
math: false
date: 2019-04-22 23:52:11
categories: Language
tags:
index_img:
banner_img:
---

### Functions

#### Definition

```dart
//三种定义方式
bool isNoble(int atomicNumber){...} //申明返回类型
isNoble(int atomicNumber){...} 			//忽略类型定义
boo isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;	//返回单行表达式结果
```

#### Parameter

```dart
//调用方法时可指定参数
enableFlags(bold: true, hidden: false);
//可选参数, device参数在函数调用时可以缺省
String say(String from, String msg, [String device]) {...}
//默认参数值
void enableFlags({bool bold = false, bool hidden = false}){...}
```

#### The main function

> 应用必须有main函数入口才能执行，main返回值为void并且有可选参数List<String>

```dart
void main(){...}
void main(List<String> arguments){
  ...
}
```

#### Functions as first-class object

> 方法可作为参数传递给另一个方法(函数式编程)，也可以将方法赋值给一个变量

```dart
var a = (msg) => '${msg.toUpperCase()}'; //方法赋值给变量

//将方法作为参数传递
var list = [1, 2, 3];

printElement(element) {
	print (element);  
}
list.forEach(printElement);
```

#### Anonymous functions(匿名方法)

> 匿名方法未给其命名，可直接调用或者将其赋值给其他变量

#### Lexical closure(词法闭包)

```dart
/// Returns a function that adds [addBy] to the
/// function's argument.
Function makeAdder(num addBy) {
  return (num i) => addBy + i;
}

main() {
  // Create a function that adds 2.
  var add2 = makeAdder(2);

  // Create a function that adds 4.
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```

---

### Operators

> 只列举独特的操作符，其余操作符与其它语言类似

| 操作符                      | 解释                                                         |
| :-------------------------- | :----------------------------------------------------------- |
| ~/                          | 除号，返回整数                                               |
| ==                          | 定义在左边对象上的函数，用于判等                             |
| as                          | 类型转换                                                     |
| is                          | 如果对象是指定的类型返回 **True**                            |
| is!                         | 如果对象是指定的类型返回 **False**                           |
| ??=                         | 若变量为null，则赋值，否则不变                               |
| ..                          | 级联操作符，用以访问一个方法返回值的成员变量                 |
| condition ?? expr1 : expr2; | 特殊算数表达式，若condition为null则执行expr1                 |
| ?.                          | 和 `.` 类似，但是左边的操作对象不能为 null。例如 foo?.ba 如果foo 为 null 则返回 null，否则返回bar |

```dart
x == y;
x .== y; 		 //==是定义以在左侧对象的函数
b ??= value; //如果 b 是 null，则赋值给 b；如果不是 null，则 b 的值保持不变
```

> 级联操作符：

```dart
querySelector('#button') // Get an object.
  ..text = 'Confirm'   	 // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
//级联调用可以嵌套
final addressBook = (new AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (new PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
//无法再void上使用级联操作
```

---

### Control flow statements

* for、forEach(与c++相同)
* switch中可用continue跳转至一个标签

```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
    executeClosed();
    continue nowClosed; // Continues executing at the nowClosed label.
nowClosed:
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}
```

---

### Class

#### Constructor(构造函数)

> 一般构造函数与语法糖

```dart
class Point{
  num x, y;
  
  Point(num x, num y){
    this.x = x;
    this.y = y;
  }
}

//使用语法糖简化构造函数
class Point{
  num x, y;
  
  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
}
```

> 命名构造函数

```dart
class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // Named constructor
  Point.fromJson(Map json) {
    x = json['x'];
    y = json['y'];
  }
}
```

> 构造函数调用顺序
>
> 1. initializer list（初始化参数列表）
> 2. superclass’s no-arg constructor（超类的无名构造函数）
> 3. main class’s no-arg constructor（主类的无名构造函数

> 调用超类的构造函数

```dart
class Person {
  String firstName;

  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // Person does not have a default constructor;
  // you must call super.fromJson(data).
  Employee.fromJson(Map data) : super.fromJson(data) {
    print('in Employee');
  }
}

main() {
  var emp = new Employee.fromJson({});

  // Prints:
  // in Person
  // in Employee
  if (emp is Person) {
    // Type check
    emp.firstName = 'Bob';
  }
  (emp as Person).firstName = 'Bob';
}
```

> 初始化列表

```dart
//通过在参数列表后加':'，通过逗号分隔表达式
class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // Initializer list sets instance variables before
  // the constructor body runs.
  Point.fromJson(Map jsonMap)
      : x = jsonMap['x'],
        y = jsonMap['y'] {
    print('In Point.fromJson(): ($x, $y)');
  }
}
```

> 重定向构造函数

```dart
//有时候一个构造函数会调动类中的其他构造函数。 一个重定向构造函数是没有代码的，在构造函数声明后，使用 冒号调用其他构造函数。
class Point {
  num x;
  num y;

  // The main constructor for this class.
  Point(this.x, this.y);

  // Delegates to the main constructor.
  //调用完此构造函数后再调用主构造函数
  Point.alongXAxis(num x) : this(x, 0);
}
```

> 常量构造函数

```dart
//如果你的类提供一个状态不变的对象，你可以把这些对象 定义为编译时常量。要实现这个功能，需要定义一个 const 构造函数， 并且声明所有类的变量为 final。
class ImmutablePoint {
  final num x;
  final num y;
  const ImmutablePoint(this.x, this.y);
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);
}
```

> Factory constructors

---



