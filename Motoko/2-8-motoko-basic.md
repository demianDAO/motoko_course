# 2.8 Motoko 基础库

## 什么是基础库(标准库)

编程语言的标准库是该语言的每种实现中都按例提供的函式库。在某些情况下，编程语言规格说明中会直接提及该函式库；另一些情况下，标准库的内容由编程社区中的非正式惯例决定。--wiki pedia

## Motoko 的基础库

### 数字类型

Int Int8 Int16 Int32 Int64 Nat Nat8 Nat16 Nat32 Nat64 Float

e8s 100,000,000

### 常用类型

Bool Char Array Text Option Result Iter None Hash

### 数据结构

Buffer List Trie TrieMap Trie Set Bolb RBtree Order

Person = {
avatar:text
name:text
}

### 系统工具

Principal Random CertifiedData Time Debug Error

### 文档地址

[Motoko Ref](https://internetcomputer.org/docs/current/references/motoko-ref/)
