# 几种语言中完全数的解法

> 原文：<https://dev.to/chenge/my-solutions-for-perfect-number-in-several-languages-1amp>

在帖子[中有一个热烈的讨论:写一个脚本来找到“完全数”](https://dev.to/peter/write-a-script-to-find-perfect-numbers-48nn)。以下是我的解决方案。

**红宝石**T2】

```
def sum_divisions(n)
  (1...n).select{|i| n % i == 0}.sum
end

def perfect_number?(n)
  n == sum_divisions(n)
end

(1..10_000).each do |i|
  puts "#{i} is perfect" if perfect_number?(i)
end 
```

**节点**

```
const sum = (accumulator, currentValue) => accumulator + currentValue;

function range(size, startAt = 0) {
  return [...Array(size).keys()].map(i => i + startAt);
}

function sumDivisions(n){
  arr = range(n-1, startAt=1).filter(x => n % x == 0)
  return arr.reduce(sum, 0);
}

function is_perfect_number(n){
  return sumDivisions(n) == n
}

range(10000, 1).forEach(function(x){
  if(is_perfect_number(x)){
    console.log(x);
  }
}) 
```

**去**T2】

```
package main

import (
  "fmt"
)

func sum_divisions(n int) int {
  sum := 0
  for i := 1; i < n; i++ {
    if n % i == 0 {
      sum += i
    }
  }
  return sum
}

func is_perfect(n int) bool {
  return n == sum_divisions(n)
}

func main() {
  for i := 1; i <= 10000; i++ {
    if is_perfect(i) {
      fmt.Println(i)
    }
  }
} 
```

**锈**

```
 fn main(){
  let perfects = (1..10_000).filter(|x| is_perfect(*x) );
  for x in perfects {
    println!("{}", x)
  }
}

fn is_perfect(n: i32) -> bool{
  n == sum_divisions(n)
}

fn sum_divisions(n: i32) -> i32{
  (1..n).filter(|x| n % x == 0).sum()
} 
```

**仙丹**

```
defmodule Play do
  def perfect?(n) do
    n == sum_divisions(n)
  end

  defp sum_divisions(n) do
    Enum.filter(1..(n-1), fn(x) -> rem(n, x) == 0 end)
    |> Enum.sum
  end
end

Enum.each(2..10_000, fn(x) -> if Play.perfect?(x), do: IO.puts(x) end) 
```

**clo jure**T2】

```
 (defn  sum_divisions  [x]  (apply  +  (filter  #(=  0  (rem  x  %))  (range  1  x))))  (defn  is_perfect  [x]  (=  x  (sum_divisions  x)))  (doseq  [i  (range  1  10000)]  (if  (is_perfect  i)  (println  i))) 
```

v2，感谢 [@jcsvveiga](https://dev.to/jcsvveiga)

```
...  (doseq  [  i  (range  1  10000)  :when  (is_perfect  i)  ]  (println  i)  ) 
```

# 结束

我是 Node，Go，Rust，Elixir 和 Clojure 的新手，如果有更好的代码，欢迎评论。

玩的开心！