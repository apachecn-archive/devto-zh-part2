# Gem minitest_lucid 上线了

> 原文：<https://dev.to/burdettelamar/gem-minitestlucid-is-up-523k>

我已经发布了 gem [minitest_lucid](https://rubygems.org/gems/minitest_lucid) ，它阐明了某些失败的 minitest 断言的数据。

这个想法是，一个失败的断言，比如说，一个大的散列可能很难被破译。所以 gem 组织结果使它更容易。

因此，代替

```
-------- expected +++ actual @@ -1 +1 @@
-{:ina=>"Alita tea loat qani.", :con=>"Et sia adid qua.", :dolupi=>"Parali coal alit qad.", :enaam=>"La ofadi aliq caat.", :qat=>"Magna voaa aaliqu maan.", :vent=>"Do velaa volor na.", :suata=>"Fadi iame adati na.", :ip=>"Utat iram adip voan.", :ex=>"Vat eiua exca inci.", :labor=>"Aut tatat proam iat.", :occaag=>"Co nulab nost paat.", :alitaaa=>"Eaam fugatia exat quis.", :cidaa=>"Enia adiaamea deseaat eliqui.", :eaalabalab=>"Aut te magna aliq.", :ut=>"Param ad dua idatu.", :ullaboa=>"Irat cona doat dunt.", :ipsum=>"Irur na nuaata doaga.", :duat=>"Noaga idat sara ex.", :anis=>"Sint adipi et aut."} +{:ina=>"Alita tea loat qani.", :con=>"Et sia adid qua.", :euaaag=>"Enta adaad idess utetu.", :enaam=>"la ofadi aliq caat.", :qat=>"Magna voaa aaliqu maan.", :vent=>"do velaa volor na.", :eaarisau=>"Taabore magn naatat in.", :ip=>"Utat iram adip voan.", :ex=>"Vat eiua exca inci.", :labor=>"aut tatat proam iat.", :esedat=>"Dola labor eu null.", :alitaaa=>"Eaam fugatia exat quis.", :cidaa=>"enia adiaamea deseaat eliqui.", :eaalabalab=>"Aut te magna aliq.", :cat=>"Cullum pa quip magat.", :ullaboa=>"Irat cona doat dunt.", :ipsum=>"Irur na nuaata doaga.", :duat=>"Noaga idat sara ex.", :eua=>"Quat comm laal inaa."} 
```

Enter fullscreen mode Exit fullscreen mode

我们得到

```
{
  :expected => {
    :class => Hash,
    :size => 19,
  },
  :actual => {
    :class => Hash,
    :size => 19,
  },
  :elucidation => {
    :missing_pairs => {
      :dolupi => 'Parali coal alit qad.',
      :suata => 'Fadi iame adati na.',
      :occaag => 'Co nulab nost paat.',
      :ut => 'Param ad dua idatu.',
      :anis => 'Sint adipi et aut.',
    },
    :unexpected_pairs => {
      :euaaag => 'Enta adaad idess utetu.',
      :eaarisau => 'Taabore magn naatat in.',
      :esedat => 'Dola labor eu null.',
      :cat => 'Cullum pa quip magat.',
      :eua => 'Quat comm laal inaa.',
    },
    :changed_values => {
      :enaam => {
        :expected => 'La ofadi aliq caat.',
        :got      => 'la ofadi aliq caat.',
      },
      :vent => {
        :expected => 'Do velaa volor na.',
        :got      => 'do velaa volor na.',
      },
      :labor => {
        :expected => 'Aut tatat proam iat.',
        :got      => 'aut tatat proam iat.',
      },
      :cidaa => {
        :expected => 'Enia adiaamea deseaat eliqui.',
        :got      => 'enia adiaamea deseaat eliqui.',
      },
    },
    :ok_pairs => {
      :ina => 'Alita tea loat qani.',
      :con => 'Et sia adid qua.',
      :qat => 'Magna voaa aaliqu maan.',
      :ip => 'Utat iram adip voan.',
      :ex => 'Vat eiua exca inci.',
      :alitaaa => 'Eaam fugatia exat quis.',
      :eaalabalab => 'Aut te magna aliq.',
      :ullaboa => 'Irat cona doat dunt.',
      :ipsum => 'Irur na nuaata doaga.',
      :duat => 'Noaga idat sara ex.',
    },
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，治疗`Hash`、`Set`和`Struct`。即将推出:`Array`和`OpenStruct`。

代码和文档在 [GitHub](https://github.com/BurdetteLamar/minitest_lucid#minitest-lucid) 上。