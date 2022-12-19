# Stan 中的可选参数/数据

> 原文：<https://dev.to/martinmodrak/optional-parametersdata-in-stan-4o33>

有时你正在开发一个有多个变量的 [Stan](http://mc-stan.org) 统计模型:也许你想在你的模型深处考虑几个不同的链接函数，或者你想在估计一个量和把它作为数据或完全不同的东西得到之间切换。在这些情况下，您可能希望使用仅适用于模型某些变体的可选参数和/或数据。遗憾的是，Stan 并不直接支持这个特性，但是您只需要一点额外的代码就可以自己实现它。在这篇文章中，我将展示如何。

## 基本型号

让我们从一个非常简单的模型开始:只是估计正态分布的均值和标准差:

```
library(rstan)
library(knitr)
library(tidyverse)
options(mc.cores = parallel::detectCores())
rstan_options(auto_write = TRUE)
set.seed(3145678)

model_fixed_code <- "
data {
  int N;
  vector[N] X;
}

parameters {
  real mu;
  real<lower=0> sigma; 
}

model {
  X ~ normal(mu, sigma);

  //And some priors
  mu ~ normal(0, 10);
  sigma ~ student_t(3, 0, 1);
}

"

model_fixed <- stan_model(model_code = model_fixed_code) 
```

让我们模拟一些数据，看看它是否符合:

```
mu_true = 8
sigma_true = 2
N = 10
X <- rnorm(N, mean = mu_true, sd = sigma_true)

data_fixed <- list(N = N, X = X)
fit_fixed <- sampling(model_fixed, data = data_fixed, iter = 500)
summary(fit_fixed, probs = c(0.1, 0.9))$summary %>% kable() 
```

|  | 意思是 | 平均标准差 | 存储卡 | 10% | 90% | 效果图 | 那 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 希腊字母表中第十二个字母 | 7.855031 | 0.0256139 | 0.5632183 | 7.162485 | 8.548415 | 483.5059 | 1.007501 |
| 希腊字母表中第十八个字母 | 1.774158 | 0.0206974 | 0.4400573 | 1.302616 | 2.350727 | 452.0508 | 1.003409 |
| lp__ | -12.103350 | 0.0555738 | 1.1132479 | -13.664610 | -11.091775 | 401.2768 | 1.004955 |

## 现在带有可选参数

假设我们现在想要处理标准差已知的情况。显然我们可以写一个新的模型。但是如果整个模型有几百行，而我们唯一想改变的是让用户指定已知的标准偏差，那该怎么办呢？最简单的解决方案是将所有变量中需要的所有参数/数据都放在周围，并使用模型块中的`if`条件来忽略其中一些，但这有点不令人满意(而且这些未使用的参数在某些情况下可能会妨碍采样)。

为了获得更好的解决方案，我们可以利用 Stan 允许零大小的数组/向量，并提供了*三元运算符* `?`。三元运算符的语法为`(condition) ? (true value) : (false value)`，工作方式类似于`if - else`语句，但在表达式中。最后一个难题是 Stan 允许数据和参数数组的大小依赖于从数据中计算出的任意表达式。可以处理已知和未知标准差的模型如下:

```
model_optional_code <- "
data {
  int N;
  vector[N] X;

  //Just a verbose way to specify boolean variable
  int<lower = 0, upper = 1> sigma_known; 

  //sigma_data is size 0 if sigma_known is FALSE
  real<lower=0> sigma_data[sigma_known ? 1 : 0]; 
}

parameters {
  real mu;

  //sigma is size 0 if sigma_known is TRUE
  real<lower=0> sigma_param[sigma_known ? 0 : 1]; 
}

transformed parameters {
  real<lower=0> sigma;
  if (sigma_known) {
    sigma = sigma_data[1];
  } else {
    sigma = sigma_param[1];
  }
}

model {
  X ~ normal(mu, sigma);

  //And some priors
  mu ~ normal(0, 10);
  if (!sigma_known) {
    sigma_param ~ student_t(3, 0, 1);
  }
}

"

model_optional <- stan_model(model_code = model_optional_code) 
```

我们不得不添加一些生物模板代码，但现在我们不必维护两个独立的模型。如果您想要在开发中测试多个变体，这个技巧有时也很有用。因为模型只编译一次，然后您可以在修改代码的其他部分时测试这两个变量，并减少等待编译的时间。

为了确保模型有效，并了解如何正确指定数据，让我们假设要估计标准偏差来拟合它:

```
data_optional <- list(
  N = N,
  X = X,
  sigma_known = 0,
  sigma_data = numeric(0) #This produces an array of size 0
)

fit_optional <- sampling(model_optional, 
                         data = data_optional, 
                         iter = 500, pars = c("mu","sigma"))
summary(fit_optional, probs = c(0.1, 0.9))$summary %>% kable() 
```

|  | 意思是 | 平均标准差 | 存储卡 | 10% | 90% | 效果图 | 那 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 希腊字母表中第十二个字母 | 7.854036 | 0.0198265 | 0.5440900 | 7.181837 | 8.531780 | 753.0924 | 0.9981102 |
| 希腊字母表中第十八个字母 | 1.730077 | 0.0152808 | 0.3918781 | 1.308565 | 2.270505 | 657.6701 | 0.9989029 |
| lp__ | -11.992770 | 0.0503044 | 0.9811551 | -13.383729 | -11.089657 | 380.4199 | 1.0016842 |

现在让我们运行模型，并给出正确的标准偏差:

```
data_optional_sigma_known <- list(
  N = N,
  X = X,
  sigma_known = 1,
  sigma_data = array(sigma_true, 1) 
  #The array conversion is necessary, otherwise Stan complains about dimensions
)

fit_optional_sigma_known <- sampling(model_optional, 
                                     data = data_optional_sigma_known, 
                                     iter = 500, pars = c("mu","sigma"))
summary(fit_optional_sigma_known, probs = c(0.1, 0.9))$summary %>% kable() 
```

|  | 意思是 | 平均标准差 | 存储卡 | 10% | 90% | 效果图 | 那 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 希腊字母表中第十二个字母 | 7.808058 | 0.0292710 | 0.6273565 | 7.017766 | 8.622762 | 459.3600 | 1.006164 |
| 希腊字母表中第十八个字母 | 2.000000 | 0.0000000 | 0.0000000 | 2.000000 | 2.000000 | 1000.0000 | 圆盘烤饼 |
| lp__ | -11.072234 | 0.0321233 | 0.6750295 | -11.917321 | -10.585280 | 441.5753 | 1.002187 |

## 延伸

显然，这种方法可以让你做各种更复杂的事情，特别是:

*   当可选参数是一个向量时，你可以有类似

`vector[sigma_known ? 0 : n_sigma] sigma;`

*   您可以有两种以上的变体可供选择，然后使用类似于

`real param[varaint == 5 ? 0 : 1];`

*   如果您的条件变得更加复杂，您可以将它们放入用户定义的函数(对于可选数据)或`transformed data`块(对于可选参数),如下所示:

```
functions {
  int compute_whatever_size(int X, int Y, int Z) {
        //do stuff
  }
}

data {
  ...
  real whatever[compute_whatever_size(X,Y,Z)];
  real<lower = 0> whatever_sigma[compute_whatever_size(X,Y,Z)];
}

transformed data {
  int carebear_size;

  //do stuff
  carebear_size = magic_result;
}

parameters {
  vector[carebear_size] carebear;
  matrix[carebear_size,carebear_size] spatial_carebear;
} 
```