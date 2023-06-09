# 避免 JavaScript 数组中的虚假值

> 原文：<https://dev.to/hugo__df/avoiding-falsy-values-in-javascript-arrays-19c7>

**TL；**博士:使用`Array.filter(Boolean)`过滤掉虚假值，避免错误。

```
// Fun function that converts a name into a
// 4 all-cap letters display name
const makeDisplayName = name =>
  (name.charAt(0) + name.slice(1).replace(/a|e|i|o|u/g, ""))
    .toUpperCase()
    .slice(0, 4);

const displayNames = [null, "orange", "apple", "mango", undefined]
  .filter(Boolean)
  .map(makeDisplayName);
// [ 'ORNG', 'APPL', 'MNG' ]

// vs
const displayNamesErrors = [null, "orange", "apple", "mango", undefined].map(
  makeDisplayName
);
// TypeError: Cannot read property 'charAt' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

# 工作原理&注意事项

`Boolean`当传递任何假值时返回`false`，当传递真值时返回`true` :

```
Boolean(false); // false
Boolean(""); // false
Boolean(null); // false
Boolean(undefined); // false
Boolean(0); // false
Boolean(NaN); // false

Boolean(1); // true
Boolean("hello"); // true
Boolean(true); // true
Boolean({}); // true
Boolean([]); // true 
```

Enter fullscreen mode Exit fullscreen mode

注意，过滤掉除`0`之外的虚假值的有效用例需要特殊处理，因为`Boolean(0)`返回`false`，因此:

```
const noFalsyEvenZero = [NaN, undefined, null, 0, 1, 2, 2000, Infinity].filter(
  Boolean
);
// [ 1, 2, 2000, Infinity ]
const noFalsyExceptZero = [
  NaN,
  undefined,
  null,
  0,
  1,
  2,
  2000,
  Infinity
].filter(el => el === 0 || Boolean(el));
// [ 0, 1, 2, 2000, Infinity ] 
```

Enter fullscreen mode Exit fullscreen mode

# 会出现这种情况的场合

通常这发生在当你想在一个对象数组上计算一些东西的时候，其中一些元素有一个属性，而其他的没有。

```
const companies = [
  {
    name: null,
    employees: [],
    founders: []
  },
  {
    name: "orange",
    employees: [],
    founders: []
  },
  {
    name: "apple",
    employees: [],
    founders: []
  },
  {
    name: "mango",
    employees: [],
    locations: []
  },
  {
    employees: [],
    founders: []
  }
];
const companyNames = companies.map(company => company.name);
// [undefined, "orange", "apple", "mango", undefined] 
```

Enter fullscreen mode Exit fullscreen mode

当您的列表中有 null 或 undefined
时，可能会出现问题

```
const makeDisplayName = name =>
  (name.charAt(0) + name.slice(1).replace(/a|e|i|o|u/g, ""))
    .toUpperCase()
    .slice(0, 4);

const companyDisplayNames = [null, "orange", "apple", "mango", undefined].map(
  makeDisplayName
);
// TypeError: Cannot read property 'charAt' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

为了避免这种情况，我们可以过滤掉 falsy 值:

```
const makeDisplayName = name =>
  (name.charAt(0) + name.slice(1).replace(/a|e|i|o|u/g, ""))
    .toUpperCase()
    .slice(0, 4);

const companyDisplayNames = [null, "orange", "apple", "mango", undefined]
  .filter(Boolean) // the identity function: `item => item` would also work here :)
  .map(makeDisplayName);
// [ 'ORNG', 'APPL', 'MNG' ]
// No Error :) 
```

Enter fullscreen mode Exit fullscreen mode

最初发表于 2018 年 4 月 11 日[codewithhugo.com](https://codewithhugo.com/avoiding-falsy-values-in-javascript-arrays/)。