---
title: TS常用泛型
date: 2020-07-09 17:04:03
tags: 
  - Typscirpt
categories:
  - 前端
---



* `Partial` 将传入的属性变为可选项
```ts
type Partial<T> = { 
    [P in keyof T]?: T[P] 
}
```


* `Required` 将传入的属性变为可选项
```ts
type Required<T> = { 
    [P in keyof T]-?: T[P]
}
```

`-?` 移除可选说明，与之对应的还有个 `+?`



* `Readonly` 将传入的属性变为只读选项
```ts
type Readonly<T> = { 
    readonly [P in keyof T]: T[P]
}
```

* `Record` 将 K 中所有的属性的值转化为 T 类型
```ts
type Record<K extends keyof any, T> = { 
    [P in K]: T 
}

// eg
type k = {
  a: string
  n: number
}

type d = Record<keyof k, string>

/* 
d = {
    a: string  
    n: string
}
*/
```

* `Pick` 从 T 中取出 一系列 K 的属性
```ts
type Pick<T, K extends keyof T> = { 
    [P in K]: T[P] 
}
// eg
type k = {
  a: string
  n: number
}

// 从k中取出a
type d = Pick<k, 'a'>

/* 
d = {
    a: string  
}
*/
```

* `Exclude`（2.8） 从 T 中找出 U 中没有的元素,或者说是 从T 中排除 U
``` ts
type Exclude<T, U> = T extends U ? never : T

type T = Exclude<1 | 2, 1 | 3> // -> 2
```

* `Extract`（2.8） 提取出 T 包含在 U 中的元素,或者说是 从 T 中提取出 U
``` ts
type Extract<T, U> = T extends U ? T : never;

type T = Extract<1 | 2, 1> // -> 1
```

* `Omit`  忽略对象某些属性
```ts
type Omit<T, K> = Pick<T, Exclude<keyof T, K>>

// 从 {name: string, age: number} 忽略 name
type Foo = Omit<{name: string, age: number}, 'name'> // -> { age: number }
```

* `ReturnType` 用它取到函数返回值的类型(infer R 就是声明一个变量来承载传入函数签名的返回值类型)
* 
```ts
type ReturnType<T> = T extends (
  ...args: any[]
) => infer R
  ? R
  : any;
  
function foo(x: number): Array<number> {
  return [x];
}
type fn = ReturnType<typeof foo>; // number[]
```