---
layout: post
title: "Swift 之类型的协变与逆变"
image: "/assets/resources/U5663P1117T4D2655F9DT20110129044829"
image-width: ""
image-height: ""
description: ""
category: 
tags: [iOS]
---
{% include JB/setup %}

Swift 之类型的协变与逆变
---
###1 什么是协变与逆变
刚开始看到协变(Covariance)和逆变(Contravariance)的时候，差点晕菜，反复查了一些资料，才稍有些自己的体会，难免有理解不对的地方，欢迎指出 :]

> 在计算机科学和类型的领域内来看，变化(variance)这个词指的是两个类型之间的关系是如何影响从它们衍生出的两种复杂类型之间的关系的。相对于原始类型，这两种复杂类型之间的关系只能是不变(invariance)，协变(covariance)和逆变(contravariance)之中的某一种。

这段比较拗口，我们一步一步拆解，既然上面提到了两个类型之间的关系，在主流的编程观念里，类型之间的关系中通常会包含子类型(subtype) 和 父类型(supertype)。

首先假设 Cat 是 Animal 的子类，就是说 Cat 是 Animal 的 subtype，可以看作上面的“原始类型”，然后有两个衍生出来的 `List<Cat>`和 `List<Animal>`类型，就是从 Cat 和 Animal 衍生出来的两种复杂类型。

那么我们就可以这么来解释协变和逆变了：
* 协变: 如果说 `List<Cat>` 也是 `List<Animal>`的subtype，也就是衍生类型的关系和原来类型（ Cat 与 Animal）的关系是一致的，那我们就说 List 是和它的原来类型协变（共同变化）的。  
* 逆变：如果说`List<Cat>` 是 `List<Animal>`的`supertype`，也就是衍生类型的关系和原来类型（ Cat 与 Animal）的关系是相反的，那我们就说 List 是和它的原来类型逆变（反变）的。
* 不变：如果说`List<Cat>` 既不是 `List<Animal>`的subtype，也不是supertype，也就是说没有关系，则说是不变的。

###2 为什么要了解协变与逆变？
我们知道 subtype 是可以替换 supertype 的，反之则不行，比如说:

```
let animal: Animal = Cat();  //Right
let cat:Cat = Animal();   //Wrong
```

来看不同返回值类型的函数替换:

```
func animalF() -> Animal { return Animal() }
func catF() -> Cat { return Cat() }

let returnsAnimal: () -> Animal = catF //Right
let returnsCat: () -> Cat = animalF //Wrong
```

第一个赋值语句通过编译是正确的 () -> Cat 和 () -> Animal 的关系与 Cat 和 Animal 之间的关系一致，也就是说是在 Swift 中函数的返回值是协变的。

再看看不同参数的函数的变化:

```
func printCat(cat: Cat) -> Void { print("\(cat)") }
func printAnimal(animal: Animal) -> Void { print("\(animal)") }

let logCat: Cat -> Void = printAnimal //Right
let logAnimal: Animal -> Void = printCat //Wrong
```

我们先不运行这段代码，从 caller 角度思考一下两个赋值语句可能的结果，假设我们要调用 logCat(Cat()) ，实际会执行 printAnimal: Animal -> Void 函数，printAnimal 是能接受 Cat 类型的参数的，运行应该没有问题。

然后如果调用 logAnimal(Animal())，实际会运行 printCat: Cat -> Void 函数，但是我们发现 printCat 理论上无法接受一个 Animal 的对象，因为它是 Cat 的父类.

我们可以看到函数 Animal -> Void 可以替换 Cat -> Void，反之行不通，也就是说 Animal -> Void 是 Cat -> Void 的 subtype，和 Animal 是 Cat 的关系是 supertype 是相反的！也就是说函数的参数是逆变的。

得到的结论是: 函数的参数是逆变的，返回值是协变的。
我们知道了变化的规则，就能判断出类型的关系，就可以知道一个类型是否可以替换另外一个类型。

思考下面这些 testCatAnimal 函数调用那些是正确的，如果把 testCatAnimal 换成 testAnimalCat 呢？

```
func testCatAnimal(f: (Cat -> Animal)) { print("cat -> animal") }

func catAnimal(cat: Cat) -> Animal { return Animal();}
func catCat(cat: Cat) -> Cat { return Cat(); }
func AnimalCat(animal: Animal) -> Cat { return Cat(); }
func AnimalAnimal(animal: Animal) -> Animal { return Animal(); }

testCatAnimal(catAnimal)
testCatAnimal(catCat)
testCatAnimal(AnimalCat)
testCatAnimal(AnimalAnimal)
```

###3. 其他类型的协变和逆变

上面我们提到了函数的参数和返回值的分别是逆变和协变，在 Swift 中除了函数，还有属性(property)，范型(Generic)等。

对于属性来说，如果是 readonly 的，属性是协变的，子类如果要覆盖，必须是父类属性的 subtype。如果是 readwrite 的，属性是不变的，子类必须和父类的属性类型完全一致。

对于范型来说，范型本身其实没有特殊的变化，它的变化与范型使用的环境紧密相关，如果是用作函数的返回值或者覆盖父类的 readonly 属性，它的协变的，如果用做函数的参数，它是逆变的，如果是用做覆盖父类的 readwrite 的属性，或者同时用做函数的返回值和参数，那它必须是不变的，也就是说范型类型必须和要求完全一致，不能使用 subtype 或者 supertype.


###4 Reference

1. [Swift 2.1 Function Types Conversion: Covariance and Contravariance](https://www.uraimo.com/2015/09/29/Swift2.1-Function-Types-Conversion-Covariance-Contravariance/)

2. [Friday Q&A 2015-11-20: Covariance and Contravariance](https://mikeash.com/pyblog/friday-qa-2015-11-20-covariance-and-contravariance.html)



