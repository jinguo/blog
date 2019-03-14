---
title: 学习-TypeScript入门
date: 2019/02/13
categories: 前端
thumbnail: http://img.janggwa.cn/typescript.jpeg
---
## 什么是 TypeScript
TypeScript 是 JavaScript 的超集，主要提供了类型系统和对 ES6 的支持。它编译出来是 JavaScript，可以运行在任何浏览器上。
<!-- more -->
### TypeScript 的优点
1. 增加了代码的可读性和可维护性（类型系统、编译报错、代码补全、接口提示等）
2. TypeScript 非常包容（类型推论、定义一切类型、兼容三方库等）
3. TypeScript 拥有活跃的社区（Angular2 是 TypeScript 编写，大部分三方库提供 TypeScript 类型定义文件）
### TypeScript 的缺点
1. 有一定的学习成本（接口、泛型等）
2. 会增加一些开发成本（多写一些类型的定义）

## TypeScript 安装
命令行： `npm install -g typescript`
安装完成后会在全局环境安装`tsc`命令，编译一个 typescript 文件直接用命令 `tsc xxx.ts` 即可。

## TypeScript 使用
### 原始数据类型
原始数据类型包括：布尔型、数值型、字符串、null、undefined、Symbol。
#### 布尔值
`let isBoolean: boolean = false;`
#### 数值
`let num: number = 1;`
#### 字符串
`let str: string = 'str';`
#### 空值
```typescript
function alertName(): void {
  alert('My Name is xxx');
}
```
#### Null 和 Undefined
```typescript
let u: undefined = undefined;
```
```typescript
let n: null = null;
```
### 任意值
任意值用来表示允许赋值为任意类型。
```typescript
let something: any = 'xxx';
something = 1;
```
### 类型推论
如果没有明确的指定类型，那么 TypeScript 会依照类型推论的规则推断出一个类型。
### 联合类型
联合类型表示取值可以为多种类型中的一种。
```typescript
let something: string | number;
something = 'xxx';
something = 1;
```
### 对象的类型--接口
在 TypeScript 中，我们使用接口来定义对象的类型。
```typescript
interface Person {
  name: string;
  age: number;
}
let ingot: Person = {
  name: 'ingot',
  age: 25
}
```
定义的接口首字母大写。
定义的变量比接口少一些属性和多一些属性都是不允许的。
#### 可选属性
```typescript
interface Person {
  name: string;
  age?: number;
}
let ingot: Person = {
  name: 'ingot'
}
```
#### 任意属性
```typescript
interface Person {
  name: string;
  [propName: string]: any;
}
let ingot: Person = {
  name: 'ingot',
  age: 11
}
```
需要注意的是，一旦定义了任意属性，那么确定属性和可选属性都必须是它的子属性。
#### 只读属性
```typescript
interface Person {
  readonly id: number,
  name: string
}
```
### 数组的类型
#### 类型+方括号表示法
```typescript
let arr: number[] = [1, 2, 3, 4, 5];
```
#### 数组泛型
```typescript
let arr: Array<number> = [1, 2, 3, 4, 5];
```
#### 用接口表示数组
```typescript
interface NumberArray {
  [index: number]: number
}
let arr: NumberArray = [1, 2, 3, 4, 5];
```
### 函数的类型
#### 函数声明
```typescript
function sum(x: number, y: number): number {
  return x + y;
}
```
注意，输入多余的（或者少于要求的）参数，是不被允许的
#### 函数表达式
```typescript
let sum:(x: number, y: number) => number = function (x: number, y: number): number {
  return x + y;
}
```

### 类型别名
我们使用 type 创建类型别名。
```typescript
type Name = string;
```