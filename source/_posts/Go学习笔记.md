---
title: Go学习笔记
date: 2019-05-15 11:05:52
categories: GO
tags: Go
---

### 变量

var 声明,支持类型判断.

`var name string`    string类型 name

`var s string`       值初始化

`var age = 20`     age 类型自动推断

`height := 165`    简短声明(仅限函数使用)

`i,j,k := 3.8,true,100` 声明一组变量

`_, res := 123,321` _特殊变量名,赋予他的值会被丢弃

### 常量
const 声明

`const Pi = 3.14` 声明一个常量Pi

```
const(
    apple = "fruit"
    banana
    )
```
banana 常量未定义初始化值会与apple值相同

### 数据类型
boolean,整型,浮点型,字符串,错误

* 布尔
    bool 初始化默认fasle

* 整型

    int8,int16,int32,int64 (有符号)
    
    uint8(byte),uint16,uint32(rune),uint64 (无符号)
    
    uintptr
    
    byte,rune 与uint8,uint32别名
    
    整形初始化默认值0
    
* 浮点型

    float32,float64(默认浮点类型)
    
    complex64,complex128
    
    float32,float64 初始化默认值0
    
* 字符串

    双引号或``,UTF8编码,\转义
    
    初始化默认值""
    
    修改需要转换类型为 rune或byte 操作后再转换
    
* 数组
 
    长度非负整数
     
    `var arr = [10]int{1,2,3,4}` 声明数组
   
* 切片 slice
    
    切片默认初始化前nil
     
    `s1 := make([]int,3,5)` 声明切片
    
    append() 尾部追加元素
    
    切片长度是包含的元素个数,
    容量是能存储的元素个数.
    
* Map 

     kV结构集合
     
     ```
     m := make(map[string]int) {
        "blue": 1,
        "red": 2
     }
     ```
     
    `delete(m,red)`  删除map中一项
    
    `m["orange"] = 3` 增加一项
    
    `m["blue"] = 4` 更新一项
    
    
*range 遍历map,slice*

    ```
    for i,v := range m {
            fmt.Println(i,v)
    }
    ```

       
    
### 函数

```
func sub(x,y int) (z int) {
    z = x - y
    return z
}
```
声明一个方法sub,参数x,y为int型, z返回参数 int型

函数无返回值可不声明,参数也可是函数.

可以传指针或传引用操作

 `...int` 表示传递变长的参数
 
 defer 关键字 在函数最后执行动作的声明(延迟代码)
 
 局部函数声明修改不影响全局,若全局有同名变量时,内部赋值会改变全局变量(非声明).


### 方法

方法是特殊的函数,区别于方法有前置实例接收参数(receiver)


### 接口

一种抽象的类型

``` 
type I interface {
    Get() int
    Put(int)
} 
```
声明时,不能有字段,不能自定义方法,只声明方法,不实习现.

```
//实现接口:
func f(p I) {
    fmt.Println(p.get())
    p.Out(1)
}
```

接收一个接口类型作为参数

p实现了接口I,Get(),Put()方法.

