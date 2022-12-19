# 遍历散列

> 原文：<https://dev.to/kreopelle/iterating-through-hashes-15jb>

作为白天的面包师和晚上的编程学生，我发现我的两个世界之间有很多联系。变量让我想起配料(type _ of _ flour =面包粉)。说明让我想起了圈圈(揉面团 10 次)。知道何时以及如何进行让我想起了条件句(如果面团很粘，加入面粉，否则如果面团很干，加入水，否则开始发酵)。

我最早的烤面包经验之一是尽可能精确地测量我的配料。当在家烘烤一两个面包时，额外的一汤匙面粉可以很容易地得到补偿，但当在复活节前烘烤 200 多个面包时，你最好做对。还有足够的其他因素(混合、醒发、定型等。)这需要注意力和判断力，我发现从正确测量我的成分开始会产生巨大的差异。

[![With all of these potential issues, why worry about measuring if you don't have to? Photo from Serious Eats](../Images/99565badc8309f9f237db868f5f0fff4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SyfnVkMj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.seriouseats.com/images/2014/11/bread-infographic2-forweb.jpg)

> 有了所有这些潜在的问题，如果没有必要，为什么还要担心测量呢？照片来自 Vicki Wasik 的严重饮食。

不幸的是，美国对他们的独立测量单位很固执，仍然更倾向于杯而不是克的精度和普遍性。杯子可能很棘手——它们不精确。我的一个杯子可能和*你的*一个杯子的容量不太一样。由于面粉有可能被压缩到杯子里，在秤上测量配料有助于消除一些猜测(你可以把所有东西都倒进碗里来检查数量)。

[![Sarah Jampel of Food52 found that there could be as much as a 37-gram difference in the volume of sugar in a measuring cup based on which cup you use.](../Images/1828c481ac06b1701d8f0db8667c4b2d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TN9Fknpy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.food52.com/bVlnUPAnkvV_c97GkJiViXD1Vxc%3D/fit-in/800x0/f4605c1a-be2e-4671-b687-b99cb589311a--IMG_6657.JPG)

> Food52 的 Sarah Jampel 发现，根据你的设定，在四分之一杯的量中，糖的体积可能有多达 37 克的差异。图片来自 Food52。

不愿意把我最喜欢的美国食谱扔到路边，但是当我想用厨房秤而不是量杯时，我相信有一种比研究、转换和写下每种成分更容易的方法——为什么不创造一种方法来帮我做这项工作呢？！

在这个实验中，我决定使用巴比·福雷的比萨饼面团，这是我快速、万无一失的晚餐。

我认为储存这些配料的最好方法是把它们混在一起。哈希是类似字典的键值对集合。就菜谱而言，键可以被认为是配料，值可以被认为是在菜谱中使用的配料的数量。

我改变了这个:

**配料**

*   3 又 1/4 杯面包粉，加上更多的面包粉用于擀面(厨师提示:使用面包粉会让你的面包皮更脆。如果你找不到面包粉，你可以用通用面粉来代替，这会让你的面包皮更有嚼劲。)
*   1 茶匙糖
*   1 个信封速溶干酵母
*   2 茶匙犹太盐
*   1 1/2 杯水，华氏 110 度
*   2 汤匙橄榄油，外加 2 茶匙

...成这样:

```
pizza_dough_ingredients = {
  bread_flour: "3.5 cups",
    sugar: "1 teaspoon",
    instant_dry_yeast: "1 envelope",
    kosher_salt: "2 teaspoons",
    water: "1.5 cups",
  olive_oil: "2 tablespoons"  
} 
```

最初，我使用一系列变量来测试我的方法，每种成分设置为等于其数量，但是，在通过一种方法运行它们之后，很难看到两者在一起。当我将数量从杯转换为克时，我希望这两个值能粘在一起，这样键-值对看起来更合理。

接下来，我研究我的方法。幸运的是，这些成分的价值都有一个相似的模式，我可以利用这个模式。它们以一个数字开始，后面是测量单位。我写了一些伪代码来描绘我想要完成的目标:

```
# go through the entire collection of ingredients 
# separate the amount from the unit of measurement
# perform math to change the value to its equivalent in grams
# put all data back together into a readable list 
```

然后我只用一种成分测试了我的过程。在遍历整个列表之前，我想确保我用来迭代散列的代码做了我想要它做的事情。根据[换算单位](https://www.convertunits.com/from/teaspoons/to/grams)，一茶匙应该等于五克。

```
 sugar = "1 teaspoon"

def gram_converter(ingredient)
  ingredient_array = ingredient.split 

# separate the quantity from the unit and save them into a new variable. #split returns an array.

  if ingredient_array[1] == "teaspoon"

# the second value of the array should be the unit of measurement. 
# this checks to determine if it is a teaspoon

    ingredient = ingredient_array[0].to_i * 5

# the first value in the ingredient array should be the quantity.
# if the value is a teaspoon, then convert it to an integer and multiply it by 5 
# if it is not converted to an integer, the quantity will just appear five times

    puts ingredient

# reveals the new value of the ingredient 

  else 
    puts "error"

# if it didn't work, lets me know so that I can go back to check my work

  end

end

gram_converter(sugar)

# => 5 
```

一旦我让方法的这一部分工作起来，我就转向散列迭代。散列迭代的工作方式类似于迭代任何其他形式的数据，只是它接受两个参数——键和值。对于这个方法，我将键设置为等于一种成分，值设置为等于它的数量。

```
 def gram_converter(ingredient_hash)
  ingredient_hash.each do |ingredient, quantity|
    # code to perform on each key-value pair
    end 
end 
```

现在，我需要说明美国食谱中常见的不同计量单位——茶匙、汤匙和杯子。我创建了一系列条件语句，如果字符串中包含给定的单位词，这些条件语句将对数量进行操作:

```
def gram_converter(ingredient_hash)

  ingredient_hash.each do |ingredient, quantity|

    quantity_array = quantity.split 

    if quantity_array[1].include?("teaspoon")
      quantity = quantity_array[0].to_i * 5

    elsif quantity_array[1].include?("cup")
      quantity = quantity_array[0].to_i * 236.58

    elsif quantity_array[1].include?("tablespoon")
      quantity = quantity_array[0].to_i * 15

    else 
      puts "error, unable to convert '#{ingredient}: #{quantity}'"

     end
  end
end 
```

其格式类似于我的测试代码:将值拆分并保存在一个新变量中，检查该成分使用的测量单位，将数量转换为整数，并乘以以克为单位的适当调整。通过设置#include？方法添加到“cup”中，它允许为操作评估“cup”和“cups”的书写。

尽管这种解决方案在某些方面工作得很好，但它有几个巨大的错误:

*   它没有考虑到一些成分可能不是整数，而是浮点数的可能性。以面粉为例，程序评估了 3 杯面粉而不是 3.5 杯，结果是 709 克面粉而不是准确的 828 克。
*   返回值不打印调整后的成分，只打印错误消息和原始散列
*   程序没有调整速溶干酵母的值

要修复这些错误:

*   我将`quantity_array[0]`转换为浮点数而不是整数，然后稍后将它返回为更悦目的整数值。*我不想丢失原始配方的值，所以我添加了一个 puts 行，以更易读的方式打印成分及其数量。我查找并添加了一行“envelope”来转换 instant_dry_yeast 的值

现在，我的代码是:

```
def gram_converter(ingredient_hash)

  puts "Ingredients converted to grams"

ingredient_hash.each do |ingredient, quantity|

quantity_array = quantity.split 

        if quantity_array[1].include?("teaspoon")
      quantity = quantity_array[0].to_f * 5
      quantity = quantity.to_i 
      puts "#{ingredient}: #{quantity} grams"

        elsif quantity_array[1].include?("cup")
      quantity = quantity_array[0].to_f * 236.58
      quantity = quantity.to_i 
      puts "#{ingredient}: #{quantity} grams"

        elsif quantity_array[1].include?("tablespoon")
      quantity = quantity_array[0].to_f * 15
      quantity = quantity.to_i 
      puts "#{ingredient}: #{quantity} grams"

        elsif quantity_array[1].include?("envelope")
      quantity = quantity_array[0].to_f * 7
      quantity = quantity.to_i 
      puts "#{ingredient}: #{quantity} grams"

        else 
      puts "error, unable to convert '#{ingredient}: #{quantity}'"

        end
  end
end 
```

我不禁觉得这段代码中有太多的重复，并记得我已经了解了一种重写条件语句的方法，以对具有不同值的代码执行相对相似的操作。情况说明来了！

我不太记得格式了，所以我在谷歌上找到了一篇关于 Skorks 的很棒的文章，带我浏览了一下。现在，我的代码执行同样的任务，但是看起来干净多了:

```
def gram_converter(ingredient_hash)

  puts "INGREDIENTS CONVERTED TO GRAMS"

  ingredient_hash.each do |ingredient, quantity|

    quantity_array = quantity.split

     case quantity_array[1]

    when "teaspoon", "teaspoons" 
      quantity = quantity_array[0].to_f * 5

     when "cup", "cups"
      quantity = quantity_array[0].to_f * 236.58

        when "tablespoon", "tablespoons"
      quantity = quantity_array[0].to_f * 15

        when "envelope", "envelopes", "packet", "packets"
      quantity = quantity_array[0].to_f * 7

     else 
      puts "error, unable to convert '#{ingredient}: #{quantity}'"
    end

        puts "#{ingredient}: #{quantity.to_i}"
        end

    puts "ORIGINAL: #{ingredient_hash}"
end 
```

所以当我调用

```
 I get: 
```

配料换算成克
糖:5
面包 _ 面粉:828
橄榄油:30
速溶 _ 干 _ 酵母:7
水:354
原单:{:糖=>“1 茶匙”、:面包 _ 面粉=>“3.5 杯”、:橄榄油=>“2 汤匙”、:速溶 _ 干 _ 酵母=>“1 信封”、:水=>“1.5 杯”}

```
 This method just scrapes the surface of useful ingredient conversion. Could I re-write it to pull the conversions from a database to include even more measurement types (such as the information for the grams in an envelope of yeast instead of adding it as another elsif statement)? How could I account for ingredients that are used multiple times (such as olive oil in the original recipe first being used in the quantity of two tablespoons and later on being used in the quantity of two teaspoons)? Is there a way to preserve ranges of quantities, such as 3.5 to 4 cups of bread flour, and have both of the values converted? How might I adjust the program to simply copy and paste the ingredient list into a user input field, and have the program itself format it into a hash before converting the data to grams?

As I learn more about programming, I hope to come back and update this method to incorporate the functionality mentioned above. For now, I'm just thrilled to have a way to easily get a list of my ingredients without spending too much time doing the math. 

*Do you have any ideas to make my code terser? Do you have any answers to the questions above? What are some of your favorite applications of programming in your daily life?* 
```