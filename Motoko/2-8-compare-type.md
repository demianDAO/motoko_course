# motoko 的数据类型

## 复合类型

一般来说，我们可以将类型分为两类：原始类型和复合类型

原始类型是:整数、自然数、浮点数、字符、文本和布尔值。

复合类型是:用户自定义数据结构, 可以构建将多种原始类型组合在一起的对象或结构。

### Tuple 元组

元组是可以包含多个元素的简单对象, 元组中也可以出现混合类型。比如定义一个个人信息:

```
type person = (Text, Text);

let me = ("Kk", "demian");
```

```
type person = (Text, Text, Nat);
//包含在圆括号中的逗号分隔的表达式列表来创建一个元组
let me = ("Kk", "demian", 22);
```

不支持单一元素元组，对于单一元素的元组，会自动变为该元素类型

```
let tup1: (Nat)= (1); // tup1类型为Nat，值为1
let tup2: (Nat,)= (1,); // tup2类型为Nat，值为1
```

元组类型支持添加组员标识符，该标识符无法作为索引，仅用于生成文档:

let tup: (add1:Nat, add2:Nat, add3:Nat) = (1,2,3);
tup.0;
tup.add1;

注意:

元组长度固定, 一旦声明，其长度不会增大或缩小。元组中的每一个位置都有一个类型，这些不同值的类型不必相同。

空元组()被称为单元类型。

#### 元组解构

pattern 匹配（pattern matching）来解构元组值, 比如:

```
let tup = (500, 6.4, 1);
var (x, y, z) = tup;
```

#### 成员访问

使用点号（.）后跟值的索引来直接访问它们。元组的第一个索引值是 0。

```
let tup: (Nat, Nat, Nat)= (1,2,3);
tup.0;
```

### Array 数组

数组用于存储多个值。与元组不同，它们只能包含一种特定类型。可以定义可变和不可变数组。区别在于可变数组可以更新而不可变数组不能。

如果需要一个允许增长和缩小长度的类似数组的集合类型，那么可以使用标准库提供的 Buffer 类型。

#### 不可变数组

`let days : [Text] = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]`

```
days[0] := "Tuesday"; // Not ok - Expected mutable target assigment
days := ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"]; // Not ok - The same problem
```

#### 可变数组

可变数组的声明与不可变数组非常相似。关键区别在于使用 var 关键字。

`var days : [Text] = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];`

`days[0] := "Tuesday"; // Not ok - expected mutable assigment target`

不能修改单个, 而是改变整个数组.

```

days := [var "Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday" ]; // Ok

public query func getDay(n : Nat) : async Text {
    return days[n];
};

```

可以使用基础库, Array.tabulate<T> 函数以及一个生成函数来创建一个数组.

```
let array1 : [Nat] = [1, 2, 3, 4, 6, 7, 8] ;

let array2 : [Nat] = Array.tabulate<Nat>(7, func(i:Nat) : Nat {
    if ( i == 2 or i == 5 ) { array1[i] * i }
    else { array1[i] }
  }) ;
```

可以使用索引来访问数组的元素.

let first = array1[0];

### Records

与数组类似，记录对象可以是可变的和不可变的.

```
type Person = {
    name : Text;
    surname : Text;
    age : Nat;
};

let person1 : Person = {
    name = "John";
    surname = "Doe";
    age = 30;
};
```

如果你想让你的对象类型可变，你应该使用 var 关键字。

```
type Person = {
    name : Text;
    surname : Text;
    var age : Nat;
};

let person1 : Person = {
    name = "John";
    surname = "Doe";
    var age = 30;
};

person1.age += 1; // Ok
person1.name := "Adam"; // Not ok, expected mutable assigment target

```

### 变体

variant 类型类似 rust 中的可带有关联数据的 enum 类型。variant 类型允许你通过列举可能的成员（variants）来定义一个类型。

```
type vari = {
  #male;
  #female;
  #; //空的variant类型
};
let va : vari = #male;

```

#### 关联数据

可以将数据直接放进 variant 成员，使得数据和 variant 成员相关联。

```
type vari = {
  #male : Nat;
  #female;
};
let va : vari = #male(1);

```

如果 variant 成员没有关联数据，那么其关联成员类型默认为单元类型()。

```
type vari = {
  #male : Nat;
  #female : ();
};
let va1 : vari = #female;
let va2 : vari = #female();
let va3 : vari = #female(());
```

注意：关联数据是不可变的。只能通过 switch 的 pattern 匹配取出关联数据。

### 可选值 option

option 类型的语法为?<type>

option 类型? type 指示该类型值可以是 null 或者具有? v 格式的值，其中 v 是 type 类型的值。

```
let a : ? Nat = ?1;
let b : ? Nat = null;
```

使用多个数据类型举例:

```
let person2 : Person = {
    name = "Charlie";
    surname = "Adams";
    var age = 28;
    var status = #single;
    var partner = ?person1; // in real situation, we should also update the status of person1 and person2 and set partner for person1 if the relationship is symmetrical
};

```

可选值在函数定义中也起着重要作用，因为函数也可以期望或返回空值。

**下节我们将函数和异步值**
