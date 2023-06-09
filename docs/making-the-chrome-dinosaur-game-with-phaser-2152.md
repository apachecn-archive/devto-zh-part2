# 用 Phaser 制作 Chrome 恐龙游戏

> 原文：<https://dev.to/aveeksaha/making-the-chrome-dinosaur-game-with-phaser-2152>

让我们创建一个经典 chrome 游戏的克隆版本，在你离线时显示出来。

# 简介

Chrome 有一个非常简单有趣的游戏，你可以在 WiFi 关闭时玩。它由一只必须跳过仙人掌的小恐龙组成。这个游戏有几个核心机制，一个当向上键被按下时会跳跃的玩家，以及无数随机生成的供玩家跳过的障碍。

这两件事都可以在 Phaser 中轻松实现，Phaser 是一个用于制作 HTML 浏览器游戏的 2D 游戏框架。我们将用几乎相同的机制创造一个游戏，但是有不同的外观。

我打算把它叫做`Runny`

如果你是 phaser 新手，我建议你阅读入门教程。

# 文件结构

```
runny
    |---assets // A folder for all our tiles and sprites
    |
    |---js // All the javascript files
    |   |--boot.js
    |   |--gameover.js
    |   |--gametitle.js
    |   |--main.js
    |   |--phaser.min.js
    |   |--preload.js
    |
    |--index.html // Where everything comes together 
```

Enter fullscreen mode Exit fullscreen mode

对于这个项目，我们将只使用来自`js folder`的`preload.js`和`main.js`

样板代码取自 [`Joshua Morony's website`](https://www.joshmorony.com/) ，下载文件 [`Here`](https://www.joshmorony.com/wp-content/uploads/2015/03/phaser-template.zip)

# 资产

我们需要三种基本的瓷砖——地面、玩家和障碍物。我将为障碍物和地面使用 2 个不同的 png 文件。对于播放器，我将使用一个精灵表，因为它可以动画。

地面和障碍瓦片由 [`Kenny`](http://kenney.nl/assets?q=2d) 制作，玩家 spritesheet 由 [`arks`](https://arks.itch.io/dino-characters) 制作。

# Preload.js

在这里，我们在使用资产之前加载我们需要的资产。在 preload.js 中，将`this.game.state.start("GameTitle");`更改为`this.game.state.start("Main");`

然后添加下面的

```
preload: function(){
    // ground tile
    this.game.load.image('tile', 'assets/tile.png'); 
    // obstacle tile
    this.game.load.image('box', 'assets/box.png'); 
    // player sprite
    this.game.load.spritesheet('player', 'assets/player.png', 24, 24, 9, -2); 
} 
```

Enter fullscreen mode Exit fullscreen mode

# Main.js

这是有趣的事情发生的地方！

### 创建

在 create 函数中，我们初始化了几个稍后会用到的变量。

```
this.tileVelocity = -450; // velocity of the obstacles
this.rate = 1500; // rate at which the obstacles appear on screen
this.jumping = false; // is the player jumping?

// add keyboard input
this.cursors = this.game.input.keyboard.createCursorKeys(); 

// set a blue background color
this.game.stage.backgroundColor = '479cde'; 

// start the phaser arcade physics system
this.game.physics.startSystem(Phaser.Physics.ARCADE); 

// execute addObstacles at the rate we set 
this.timer = game.time.events.loop(this.rate, this.addObstacles, this); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要两组，一组在地面上，一组在障碍物上。

```
// the floor group
this.floor = this.game.add.group();
this.floor.enableBody = true;
this.floor.createMultiple(Math.ceil(this.game.world.width / this.tileWidth), 'tile');

// the obstacle group
this.boxes = this.game.add.group();
this.boxes.enableBody = true;
this.boxes.createMultiple(20, 'box');
this.game.world.bringToTop(this.floor)
this.addBase(); // add the ground for the player to run on
this.createPlayer(); // add the player to the game 
```

Enter fullscreen mode Exit fullscreen mode

`Final create function`

```
create: function() {

    this.tileVelocity = -450;
    this.rate = 1500;
    this.jumping = false;

    this.tileWidth = this.game.cache.getImage('tile').width;
    this.tileHeight = this.game.cache.getImage('tile').height;  
    this.boxHeight = this.game.cache.getImage('box').height;

    this.game.stage.backgroundColor = '479cde';

    this.game.physics.startSystem(Phaser.Physics.ARCADE);

    this.floor = this.game.add.group();
    this.floor.enableBody = true;
    this.floor.createMultiple(Math.ceil(this.game.world.width / this.tileWidth), 'tile');

    this.boxes = this.game.add.group();
    this.boxes.enableBody = true;
    this.boxes.createMultiple(20, 'box');
    this.game.world.bringToTop(this.floor)

    this.addBase();
    this.createPlayer();
    this.cursors = this.game.input.keyboard.createCursorKeys(); 

    this.timer = game.time.events.loop(this.rate, this.addObstacles, this);

}, 
```

Enter fullscreen mode Exit fullscreen mode

### 添加基数

现在我们的玩家需要一个平台来运行。地面实际上不会移动，所以我们可以根据屏幕的大小设置固定数量的瓷砖。让我们加上基数。

```
addBase: function () {

    // calculate how many tiles are needed
    var tilesNeeded = Math.ceil(this.game.world.width / this.tileWidth); 

    // the tiles should be at the bottom of the screen
    var y = (this.game.world.height - this.tileHeight); 

    for (var i = 0; i < tilesNeeded; i++) {

        // add one tile after the other
        var x = i * this.tileWidth; 
        var tile = this.floor.getFirstDead();
        tile.reset(x, y); // set the x and y coordinates
        tile.body.immovable = true;

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 创建播放器

既然玩家现在有东西站在上面，我们可以继续创建玩家。

```
createPlayer: function () {

    // spawn the player a to the left and a little above the ground
    this.player = this.game.add.sprite(this.game.world.width/5, this.game.world.height -(this.tileHeight*2), 'player'); 

    // depends on the size of your sprite
    this.player.scale.setTo(4, 4); 
    this.player.anchor.setTo(0.5, 1.0);

    // enable arcade physics on the player
    this.game.physics.arcade.enable(this.player); 

    // the player has to fall down once it jumps
    this.player.body.gravity.y = 2200; 
    this.player.body.bounce.y = 0.1;
    this.player.body.drag.x = 150;

    // since it is a sprite sheet, you can set animations
    var walk = this.player.animations.add('walk'); 

    // play the walk animation at 20fps
    this.player.animations.play('walk', 20, true); 
} 
```

Enter fullscreen mode Exit fullscreen mode

### 添加障碍

仅仅让玩家跳而没有任何东西可以跳过会很无聊，所以我们要添加一些盒子。如果你还没有意识到，我们还没有给玩家一个速度。这是因为玩家实际上不会移动，我们可以简单地通过给障碍物一个玩家方向的速度来制造移动的假象。

同样为了保持游戏的趣味性，玩家必须跳过的障碍物的高度是随机的，基于此，玩家可以单跳或双跳。稍后我们将实现跳转功能。

```
addObstacles: function () {

    // Randomly decide how tall the stack of boxes is going to be
    // maximum number of tiles that the player can jump over is 4
    var tilesNeeded = Math.floor( Math.random() * (5 - 0));

    // slowly increase the difficulty by increasing how often boxes spawn and how fast they move
    if (this.rate > 200) {
        this.rate -= 10;
        this.tileVelocity = -(675000 / this.rate);
    }

    // Add the boxes to the game
    for (var i = 0; i < tilesNeeded; i++) {
        // we want the boxes to be created just outside the right side of the screen
        this.addBox(this.game.world.width , this.game.world.height - this.tileHeight - ((i + 1)* this.boxHeight ));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数实际上并没有将盒子添加到地图上，这是由`addBox`完成的，它在给定的 x 和 y 坐标上创建了一个盒子。

```
addBox: function (x, y) {

    // get the boxes that have already been moved outside the screen
    var tile = this.boxes.getFirstDead();

    tile.reset(x, y);
    // set the velocity of the set of boxes
    tile.body.velocity.x = this.tileVelocity;
    tile.body.immovable = true;
    tile.checkWorldBounds = true;
    // destroy them when they go outside the screen
    tile.outOfBoundsKill = true;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 运动

现在我们需要给玩家一个方法，当我们按下向上箭头时，玩家可以跳过盒子。这由`update`功能负责，该功能可持续检查输入。我们还将实现前面提到的双跳转机制。这不是原游戏的一部分，但使它更有趣。我们还将检查其他瓷砖和玩家之间的碰撞。如果玩家碰到一个盒子，游戏就结束了。

```
update: function() {

    // collide with the floor
    this.game.physics.arcade.collide(this.player, this.floor);
    // collide with the boxes and call gameOver when the player hits a box
    this.game.physics.arcade.collide(this.player, this.boxes, this.gameOver, null, this);

    // implementing the double jump
    var onTheGround = this.player.body.touching.down;

    // If the player is touching the ground, let him have 2 jumps
    if (onTheGround) {
        this.jumps = 2;
        this.jumping = false;
    }

    // Jump!
    if (this.jumps > 0 && this.upInputIsActive(5)) {
        this.player.body.velocity.y = -1000;
        this.jumping = true;
    }

    // Reduce the number of available jumps if the jump input is released
    if (this.jumping && this.upInputReleased()) {
        this.jumps--;
        this.jumping = false;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要实现两个方法，一个用于检查向上箭头是否被按住，另一个用于检查它是否被释放。

```
// This function returns true when the player presses the "jump" control
upInputIsActive: function (duration) {
    var isActive = false;

    isActive = this.input.keyboard.downDuration(Phaser.Keyboard.UP, duration);
    isActive |= (this.game.input.activePointer.justPressed(duration + 1000 / 60) &&
        this.game.input.activePointer.x > this.game.width / 4 &&
        this.game.input.activePointer.x < this.game.width / 2 + this.game.width / 4);

    return isActive;
},

// This function returns true when the player releases the "jump" control
upInputReleased: function () {
    var released = false;

    released = this.input.keyboard.upDuration(Phaser.Keyboard.UP);
    released |= this.game.input.activePointer.justReleased();

    return released;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 游戏结束

对于我们项目的最后一部分，当玩家击中一个盒子，游戏结束时，我们需要做一些事情。在这种情况下，因为我们没有开始屏幕，我们将再次加载游戏，即`main.js`所以把这一行放在`gameOver`

```
this.game.state.start('GameOver'); 
```

Enter fullscreen mode Exit fullscreen mode

# 代号&演示

如果你想要这个项目的代码，叉这个 GitHub repo - [`Dinosaur game code`](https://github.com/Aveek-Saha/Chrome-dinosaur-game-clone)

查看这里的代码，自己玩- [`Live demo`](https://aveeksaha.gitlab.io/runny/)