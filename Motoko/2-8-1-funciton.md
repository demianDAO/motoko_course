# 特殊类型 - 函数和异步

## 什么是函数?

在计算机函数中，函数是指一段在一起的、可以做某一件事儿的程序。也叫做子程序、（OOP 中）方法。

在计算机语言中把大段代码拆成函数，通过一层一层的函数调用，就可以把复杂任务分解成简单的任务，这种分解可以称之为面向过程的程序设计。函数就是面向过程的程序设计的基本单元, 并且可以高度重用代码,省的重复造轮子和写更多代码!

## 函数式编程

维基百科的定义：“函数式编程是一种编程范式，它将电脑运算视为函数运算，并且避免使用程序状态以及易变对象。其中，λ 演算是该语言最重要的基础。而且 λ 演算的函数可以接受函数作为输入的参数和输出的返回值。”

函数式编程（请注意多了一个“式”字）——Functional Programming，虽然也可以归结到面向过程的程序设计，但其思想更接近数学计算。

我们首先要搞明白计算机（Computer）和计算（Compute）的概念。

在计算机的层次上，CPU 执行的是加减乘除的指令代码，以及各种条件判断和跳转指令，所以，汇编语言是最贴近计算机的语言。

而计算则指数学意义上的计算，越是抽象的计算，离计算机硬件越远。

对应到编程语言，就是越低级的语言，越贴近计算机，抽象程度低，执行效率高，比如 C 语言；越高级的语言，越贴近计算，抽象程度高，执行效率低，比如 Lisp 语言。

函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。

函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

## Motoko 函数

简单函数: `Int.toText : Int -> Text`

接受参数和返回值:`divRem : (Int, Int) -> (Int, Int)`

范行:`Option.unwrapOr : <T>(?T, default : T) -> T`

一等函数, 可以传资源:

```
map : <A, B>(f : A -> B, xs : [A]) -> [B]
 let funcs : [<T>(T) -> T] = …
```

### 函数声明

一般分为: 公共函数和私有函数

```

// 默认 public
func add(x : Int, y : Int) : Int = x + y;

func applyNTimes<T>(n : Int, x : T, f : T -> ()) {
  if (n <= 0) return;
  f(x);
  applyNTimes(n-1, x, f);
};

applyNTimes<Text>(3, "Hello!", func(x) { Debug.print(x) } );

```

私有函数,使用 private 定义

```

private func addNum(x: Int, y: Int) : Int  = x + y;

```

## object 类型

Object 类型的语法为 `<sort>? { <typ-field>;* }`。通过列出其零个或多个命名类型字段来指定 Object 类型。在一个 Object 类型中，字段的名称必须是不同的，仅在字段顺序上不同的 Object 类型是等效的。

在 Object 声明中，只有 public 的字段才是该 Object 的类型字段，privae 字段不是该 Object 的类型字段（可以认为内部变量），这会影响子类型的判断。例如：

```
object counter {
  public var count = 0;
  public func inc() { count += 1 };
};
// 类型为{var count : Nat; inc : () -> ()}
object counter {
  var count = 0;
  public func inc() { count += 1 };
};
// 类型为{inc : () -> ()}
```

### object 解构

对象 pattern `"{ <pat-field>;* }"` 将对象值(public 命名字段值的集合)与命名 pattern 字段序列进行匹配。对象 pattern 的每个字段所绑定的标识符必须是不同的。对象 pattern 中的 pattern 字段的名称必须不同。

pat-filed 有以下两种新式：
`<id> (: <typ>) = <pat>`：等号左边指定对象的命名字段。等号右边仍是一个 pattern 匹配，其与等号左边的字段值进行匹配。
`<id> (: <typ>)`：简洁的形式，等价于`<id>(: <typ>) = <id>(: <typ>)`。
其中：如果不需要使用绑定的标识符，如果无需匹配 type，则可以省略 typ。因为要使用标识符，编译器需要知道标识符的类型。

例如：

```
func fullName({ first : Text; mid : Text; last : Text }) : Text {
  first # " " # mid # " " # last
};
fullName(object {
  public  let first = "1";
  public  let mid = "2";
  public  let last = "3";
})
```

## actor 类型

像 object 类型, 但标记为 Actor

```
type Broadcast = actor {
  register : Receiver -> (); //单向不等待
  send : Text -> async Nat; // 更新
};

type Receiver = actor {
  recv : query Text -> async Nat //查询
};
```

可分享的类型: 所有原始类型、不可变记录、元组、数组、变体、选项、actor 类型、shared 功能类型!

不可分享的类型: 可变的东西、局部函数、对象（带有方法）!

一个完整的 actor

```
import Array "mo:base/Array";

actor Broadcast {
  type Receiver = actor {recv : query Text -> async Nat};

  var r : [Receiver] = [];

  public func register(a : Receiver) {
    r := Array.append(r, [a]);
  };

  public func send(t : Text) : async Nat {
    var sum = 0;
    for (a in r.vals()) {
      sum += await a.recv(t);
    };
    return sum;
  };
}


```

### actor 导入

```

import Broadcast "canister:Broadcast";
/* or
import Broadcast "ic:r7inp-6aaaa-aaaaa-aaabq-cai";
*/
actor Self {

  var count = 0;

  public func go() {
    Broadcast.register(Self);
  };

  public query func recv(msg : Text) : async Nat {
    return count;
  }
}


```

## 异步和 await

当容器或参与者在 Internet 计算机上相互通信时，它是异步发生的。消息以一种孤立的方式一个接一个（按顺序）处理。

```
let result1 = await service1.computeAnswer(params);
let result2 = await service2.computeAnswer(params);
finalStep(result1, result2)
```

### await

等待计算返回的结果：
如果结果是一个值，则继续该值，
如果结果是一个 Error，throw 则返回错误。

```
public func send(t : Text) : async Nat {
    var sum = 0;
    for (a in r.vals()) {
      sum += await a.recv(t); // may return Nat or `throw` error
    };
    return sum;
  };
```

## stable 类型

stable 变量是一种在声明时使用的类型, 如果要想在升级代码后继续保障原有数据不被清除,需要在声明类型时给变量为 stable 类型,同时需要调用系统函数 preupgrade 和 postupgrade

```
import Array "mo:base/Array";

actor Broadcast {

  type Receiver = actor {recv : query Text -> async Nat};

  stable var r : [Receiver] = []; // declare r `stable`

  public func register(a : Receiver) { … }
  public func send(t : Text) : async Nat { … }

  // optional pre-upgrade action
  system func preupgrade() { Debug.print("saving receivers"); }

  // optional post-upgrade action
  system func postupgrade() {  Debug.print("restoring receivers"); }
}

```

### 参考

[聊一聊|计算机函数](https://www.liaoxuefeng.com/wiki/1016959663602400/1017328525009056)

[深入理解函数式编程（上）](https://tech.meituan.com/2022/10/13/dive-into-functional-programming-01.html)
