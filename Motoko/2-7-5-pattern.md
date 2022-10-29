# 匹配模式

## 通配符

通配符 pattern "\_"用于匹配单个值而不将其内容绑定到任何标识符。

## 标识符

标识符 pattern "id"匹配单个值并将其绑定到标识符 id。

## 字面量

字面量 pattern 将单个值与字面量常量值匹配，如果它们不是相等的值，则失败。

## 类型注解

注解`pattern "<pat> : <typ>"`将`<typ>`类型的值与`<pat>`相匹配。

类型注解 pattern 不是动态类型检测，主要用于约束`<pat>`中绑定的标识符的类型，例如在函数的参数模式以及 let 标识符绑定中。

## 元组

元组`pattern "( <pat>,* )"`将 n 元组值与 n 元组 pattern 进行匹配（元组值和元组 pattern 必须具有相同数量的 item）。元组 pattern 的每个 item 绑定的标识符必须是不同的。只有所有的 item 都成功匹配，整个元组 pattern 才算成功匹配。

空元组`pattern ()`称为单元 pattern。

## Object

对象`pattern "{ <pat-field>;* }"`将对象值(public 命名字段值的集合)与命名 pattern 字段序列进行匹配。对象 pattern 的每个字段所绑定的标识符必须是不同的。对象 pattern 中的 pattern 字段的名称必须不同。

- pat-filed 有以下两种新式：
  - `<id> (: <typ>) = <pat>：`等号左边指定对象的命名字段。等号右边仍是一个 pattern 匹配，其与等号左边的字段值进行匹配。
  - `<id> (: <typ>)：`简洁的形式，等价于`<id>(: <typ>) = <id>(: <typ>)`。

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

## Variant

`variant pattern "# <id> <pat>?"`将形式为# `<id'>`v 的 variant 值与 variant pattern 进行匹配。如果`<pat>`省略了，那么是单元`pattern (())`的简写。

如果 variant 值的标签 <id'> 与 pattern 的 <id> 不同（即 <id> <> <id'>），则 pattern 匹配失败。如果标签相等但 variant 值 v 与 pattern <pat>? 不匹配，同样 pattern 匹配失败。

## Option

`option pattern "? <pat>"`用于匹配`? <typ> option`类型的值。

如果 option 值为 null，则匹配失败。如果值为 ? v，那么匹配? <pat>的结果是 v 与 <pat> 匹配的结果。

字面量 pattern null 可以用于匹配 option 类型的值是否为 null。

## Or

`or pattern "<pat1> or <pat2>"`是分离匹配 pattern。

当一个值与`<pat1> or <pat2>`匹配时，如果`<pat1>`匹配成功，则返回`<pat1>`匹配结果，如果`<pat1>`匹配失败，则返回`<pat2>`匹配结果。

注意：`<pat1> 和 <pat2>`都不能包含标识符`(<id>) pattern`，因此成功的匹配总是绑定零标识符.
