# 务实的类型:不透明的类型和他们如何拯救火星气候轨道器

> 原文：<https://dev.to/stereobooster/pragmatic-types-opaque-types-and-how-they-could-have-saved-mars-climate-orbiter-1551>

> 正如美国国家航空航天局此前宣布的那样，飞船失事的“根本原因”是在一个与导航相关的地面任务软件中，英制单位未能转化为公制单位。
> 
> - [来源](https://mars.jpl.nasa.gov/msp98/news/mco991110.html)

这听起来几乎不切实际——一个软件错误导致了航天器的损失。但这是真的，开发者忘记了将一种类型的单位转换成另一种类型的单位。

如何确保你不会把米加到英里或米加到秒或秒加到小时或欧元加到美元？类型系统对此有一个答案——不透明类型。

## 流量

Imperial.js:

```
// @flow
export opaque type Mile = number;
export const numberToMile = (n: number): Mile => n; 
```

Enter fullscreen mode Exit fullscreen mode

Metric.js:

```
// @flow
export opaque type Kilometer = number;
export const numberToKilometers = (n: number): Kilometer => n; 
```

Enter fullscreen mode Exit fullscreen mode

test.sj

```
//@flow
import { type Kilometer } from './Metric'
import { numberToMile } from './Imperial'
export const calculateOrbit = (n: Kilometers) => {
  // do some math here
  n;
};

let m = numberToMile(123);
calculateOrbit(m); 
```

Enter fullscreen mode Exit fullscreen mode

错误:

```
Cannot call calculateOrbit with m bound to n because Mile [1] is incompatible with Kilometers [2].

     test.js
 [2]  4│ export const calculateOrbit = (n: Kilometer) => {
      5│   // do some math here
      6│   n;
      7│ };
      8│
      9│ let m = numberToMile(123);
     10│ calculateOrbit(m);
     11│ 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:只有当你在单独的文件中保存`Mile`和`Kilometer`的定义时，这才会起作用。

## 打字稿

TypeScript 中没有原生的不透明类型，但是你可以使用[一个由 Charles Pick](https://codemix.com/opaque-types-in-javascript/) :
提出的解决方案

```
type Opaque<K, T> = T & { __TYPE__: K }; 
```

Enter fullscreen mode Exit fullscreen mode

[例子](https://www.typescriptlang.org/play/#src=type%20Opaque%3CK%2C%20T%3E%20%3D%20T%20%26%20%7B%20__TYPE__%3A%20K%20%7D%3B%0D%0A%0D%0Atype%20Kilometer%20%3D%20Opaque%3C'Kilometers'%2C%20number%3E%3B%0D%0Atype%20Mile%20%3D%20Opaque%3C'Mile'%2C%20number%3E%3B%0D%0Aconst%20numberToMile%20%3D%20(n%3A%20number)%20%3D%3E%20n%20as%20Mile%3B%0D%0Aconst%20calculateOrbit%20%3D%20(n%3A%20Kilometer)%20%3D%3E%20%7B%0D%0A%20%20%2F%2F%20do%20some%20math%20here%0D%0A%20%20n%3B%0D%0A%7D%3B%0D%0A%0D%0Alet%20m%20%3D%20numberToMile(123)%3B%0D%0AcalculateOrbit(m)%3B%0D%0A) :

```
type Kilometer = Opaque<'Kilometers', number>;
type Mile = Opaque<'Mile', number>;
const numberToMile = (n: number) => n as Mile;
const calculateOrbit = (n: Kilometer) => {
  // do some math here
  n;
};

let m = numberToMile(123);
calculateOrbit(m); 
```

Enter fullscreen mode Exit fullscreen mode

错误:

```
Argument of type 'Opaque<"Mile", number>' is not assignable to parameter of type 'Opaque<"Kilometers", number>'.
  Type 'Opaque<"Mile", number>' is not assignable to type '{ __TYPE__: "Kilometers"; }'.
    Types of property '__TYPE__' are incompatible.
      Type '"Mile"' is not assignable to type '"Kilometers"'. 
```

Enter fullscreen mode Exit fullscreen mode

[这篇文章是](https://dev.to/t/pragmatictypes)系列文章的一部分。在[推特](https://twitter.com/stereobooster)和 [github](https://github.com/stereobooster) 上关注我