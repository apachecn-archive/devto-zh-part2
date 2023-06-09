# Repasando Artículos:为什么函数式编程很重要

> 原文：<https://dev.to/gustavo94/repasando-artculos-why-functional-programming-matters---1l7i>

d .特纳，艾迪森-韦斯利，1990 年

随着时间的推移，软件变得越来越复杂，模块化对于确保代码质量和便于维护的重要性日益明显，因为一个小模块更易于开发、重新使用和测试；但是，模块化产生了一种新的需要，即改进“glue code”，即用于连接不同模块的代码。功能编程的主要优点是，通过参考透明度使每个模块更易于收集和测试，并更有效地将模块与执行高级别和粗略评估功能结合起来，从而改进模块化。

代码示例是用 JAVA 编写的，使用的是实现功能编程大部分特性的“[var](https://www.vavr.io)库。

## 基准透明度

在函数编程中，函数与数学函数共享其定义，数学函数的一个主要特征是，对于给定的输入参数，函数总是返回相同的值，也就是说，它们消除了其它编程范例中存在的副作用。因此，对函数的调用可以替换为函数的值，而不会影响执行顺序或执行上下文中隐含的任何其他因素，因此需要创建更易于理解、重复使用和测试的模块。

## 高阶函数

高阶函数是接收或传回一或多个函数的函数，可让您重复使用程式码，并针对不同的资料类型推广某些行为，而这些资料类型只需重写定义其特定行为的基本函数，以及较短且容易理解的「glue 程式码」。高阶函数的一个基本范例是 foldLeft 函数，它可让您将清单中的所有项目组合成单一的结果项目，方法是重复套用任何从清单中项目类型接收物件参数的函数。

```
 public static void main(String[] args) {
      List<Integer> list = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9);
      Integer r = list.foldLeft(0, Integer::sum); // 45
  }

  //codigo de la funcion foldLeft tomado del código fuente de vavr
  default <U> U foldLeft(U zero, BiFunction<? super U, ? super T, ? extends U> f) {
        Objects.requireNonNull(f, "f is null");
        U xs = zero;
        for (T x : this) {
            xs = f.apply(xs, x);
        }
        return xs;
    } 
```

## 懒惰评价

不产生副作用的好处之一是，程序员可以使用懒惰的评估，而不必预测可能出现的无数种情况，这就是定义函数的结果值，在程序执行过程中不需要计算这些值，从而可以在中创建行为，如理论上无限长的安排使用懒惰评估的一个例子是，使用被评估的候选人的无限序列计算一个数字的平方根，直到找到一个符合所需精度的候选人。

```
 public static Function1<Double, Double> siguienteCandidato(Double n){
      /**
       * Formula para calcular la sucesión de candidatos a para la raíz cuadrada de n según el método de Newton-Raphson
       */
      return Function1.of((anterior) -> (anterior + (n / anterior)) / 2d);
  }

  public static Double calcularRaizCuadrada(Double n){
      Stream<Double> s = Stream.iterate(1d, siguienteCandidato(n));
      Double epsilon = 0.0001d; // Se utiliza para especificar la precisión
      return s.takeUntil(candidato -> Math.abs((candidato * candidato) - n) < epsilon).last();
  }

  public static void main(String[] args) {
      System.out.println(calcularRaizCuadrada(25d)); // 5.000023178253949
  } 
```

在上例中，您还可以看到由返回另一个函数的函数组成的下一个候选函数的第二种情况。