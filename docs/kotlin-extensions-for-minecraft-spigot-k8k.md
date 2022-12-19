# 《我的世界》龙头的科特林扩展

> 原文：<https://dev.to/reyadayer/kotlin-extensions-for-minecraft-spigot-k8k>

《我的世界》服务器 MOD 龙头是有用的，但有一些不方便的方法。让我们用 Kotlin 扩展来改变它们。

1.  Drop ItemStack

您可以使用下面的代码在世界中放下物品，但这是多余的。

```
val location = player.location
location.world.dropItem(location, itemStack) 
```

Enter fullscreen mode Exit fullscreen mode

让我们用以下扩展创建一个基于位置的方法。

LocationExtensions.kt

```
fun Location.dropItem(itemStack: ItemStack) {
    world.dropItem(this, itemStack)
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  给予项目堆栈

你可以给下面的玩家物品。

```
val itemStack = ItemStack(Material.IRON_SWORD)
player.inventory.addItem(itemStack) 
```

Enter fullscreen mode Exit fullscreen mode

但是，当玩家的库存满了，你就不能给物品了。

在那个时候，定义下面的扩展并把它们扔到地上是仁慈的。

PlayerExtensions.kt

```
fun Player.giveItem(itemStack: ItemStack) {
    if (inventory.firstEmpty() == -1) {
        player.location.dropItem(itemStack)
    } else {
        player.inventory.addItem(itemStack)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  播放声音

用同样的方法做它。

```
location.world.playSound(location, sound, volume, pitch) 
```

Enter fullscreen mode Exit fullscreen mode

LocationExtensions.kt

```
fun Location.playSound(sound: Sound, volume: Float, pitch: Float) {
    this.world.playSound(this, sound, volume, pitch)
} 
```

Enter fullscreen mode Exit fullscreen mode

spawnParticle 可以用同样的方式简化。