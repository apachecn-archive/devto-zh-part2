# 让我们建立一个锈前端与紫杉-第 3 部分

> 原文：<https://dev.to/deciduously/lets-build-a-rust-frontend-with-yew---part-3-ch3>

## 游戏开始

这是 3 部分系列的第三部分，也是最后一部分。这篇文章从一个看起来有点像 [this](https://github.com/deciduously/hunt-the-wumpus/tree/master/part2) 的项目开始。如果你需要赶上进度，这里有[第一部分](https://dev.to/deciduously/lets-build-a-rust-frontend-with-yew---part-1-3k2o)和[第二部分](https://dev.to/deciduously/lets-build-a-rust-frontend-with-yew---part-2-1ech)的链接。

第 2 部分给我们留下了一个可以四处游荡的洞穴，但没有太多的危险。这个游戏的名字是“追捕万普斯”，但是一个万普斯都没有！

打开`src/lib.rs`。让我们给我们的`Model`加一个:

```
pub struct Model {
  arrows: u8,
  current_room: u8,
  messages: Vec<String>,
  wumpus: u8,
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个占位符开始位置-没有房间 0，我们的洞穴房间是 1-索引:

```
fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
  let mut rng = thread_rng();
  let mut ret = Model {
    arrows: 5,
    current_room: 1,
    messages: Vec::new(),
    wumpus: 0,
  };
  // ..
} 
```

Enter fullscreen mode Exit fullscreen mode

我们一会儿就把他安置好。不过，这还不够可怕。除了四处游荡的饥饿的怪物外，还有两只巨大的蝙蝠。如果你最终和一只蝙蝠待在一个房间里，它会把你彻底打败，然后把你扔进洞穴的其他地方。

现在我们要把恐怖调到 11 点。忘了那两个引发混乱的地狱蝙蝠吧。还有两个房间是无底洞。怎么回事，伙计。**无底**。你会渴死的，经过三天的坠落。给我罪犯，我会告诉你怎么做。

我们也会跟踪他们的:

```
pub struct Model {
  arrows: u8,
  current_room: u8,
  messages: Vec<String>,
  wumpus: u8,
  bats: [u8; 2],
  pits: [u8; 2],
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续为`Model`实现`Default`，并为我们稍后可以配置的所有内容添加一些零:

```
impl Default for Model {
  fn default() -> Self {
    Self {
      arrows: 5,
      current_room: 1,
      messages: Vec::new(),
      wumpus: 0,
      bats: [0, 0],
      pits: [0, 0],
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我们将使用一个助手函数来生成随机数，避免使用我们指定的列表。

我们将调用 JS 来生成随机数。首先在`lib.rs` :
中的`extern crate stdweb`行添加`#[macro_use]`注释

```
#[macro_use]
extern crate stdweb;
#[macro_use]
extern crate yew; 
```

Enter fullscreen mode Exit fullscreen mode

我不想把`lib.rs`弄得太乱，所以我们创建一个名为`src/util.rs` :
的文件

```
use stdweb::unstable::TryInto;

pub fn js_rand(bottom: u8, top: u8) -> u8 {
  let rand = js! { return Math.random(); };
  let base: f64 = rand.try_into().unwrap();
  (base * top as f64).floor() as u8 + bottom
}

pub fn gen_range_avoiding(bottom: u8, top: u8, avoid: Vec<u8>) -> u8 {
  let mut ret = avoid[0];
  while avoid.contains(&ret) {
    ret = js_rand(bottom, top);
  }
  ret
} 
```

Enter fullscreen mode Exit fullscreen mode

`js_rand`函数包装了我们的互操作，所以我们尽可能多地处理 Rust 类型——我们只需要 JS 作为熵。帮手`gen_range_avoiding`会还给我们一个`avoid`里没有出现的`u8`。

我们也可以将`lib.rs`中的`room_exits`移动到这个文件中，并标记为`pub`。别忘了加到`lib.rs` :
的最上面

```
mod components;
mod util;

use self::{
  components::{controls::Controls, messages::Messages, stats::Stats},
  util::*,
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了让这个工具更容易使用，让我们在`lib.rs`中给`Model`一个方法，以及一个`configure_cave()`方法来初始化我们的世界并放置我们所有的虐待狂陷阱:

```
impl Model {
  fn configure_cave(&mut self) {
    self.messages.push(
      "You've entered a clammy, dark cave, armed with 5 arrows.  You are very cold.".to_string(),
    );
    self.wumpus = js_rand(1, 20);
    self.bats[0] = self.get_empty_room();
    self.bats[1] = self.get_empty_room();
    self.pits[0] = self.get_empty_room();
    self.pits[1] = self.get_empty_room();
    self.warning_messages();
  }

  fn get_empty_room(&self) -> u8 {
    gen_range_avoiding(
      0,
      20,
      vec![
        self.current_room,
        self.wumpus,
        self.bats[0],
        self.bats[1],
        self.pits[0],
        self.pits[1],
      ],
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以重写我们的`create`函数:

```
fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
  let mut ret = Model::default();
  ret.configure_cave();
  ret
} 
```

Enter fullscreen mode Exit fullscreen mode

所有这些危险都潜伏在每个角落，我们应该在玩家走动时给他们一些警告。

让我们给`Model`增加另一种方法来嗅我们周围的环境。如果我们任何一个相邻的房间有危险，我们会用一个怪异的信息提醒玩家。将此添加到`impl Model`模块:

```
fn warning_messages(&mut self) {
  for adj in &room_exits(self.current_room).unwrap() {
    let t = *adj;
    if self.wumpus == t {
      self
        .messages
        .push("You smell something horrific and rancid.".into());
    } else if self.pits.contains(&t) {
      self
        .messages
        .push("You feel a cold updraft from a nearby cavern.".into());
    } else if self.bats.contains(&t) {
      self
        .messages
        .push("You hear a faint but distinct flapping of wings.".into());
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

无论何时移动，我们都可以检查附近的危险:

```
fn update(&mut self, msg: Self::Message) -> ShouldRender {
  match msg {
    Msg::SwitchRoom(target) => {
      self.current_room = target;
      self.messages.push(format!("Moved to room {}", target));
      self.warning_messages();
      true
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们开始处理更大的能级态之前，让我们从`Model`中抽象出`Game`。创建一个名为`src/game.rs`的新文件。我们将提取许多我们在`Model`上定义的逻辑，放在这里。

```
use crate::util::*;

pub struct Game {
  pub arrows: u8,
  pub current_room: u8,
  pub messages: Vec<String>,
  pub wumpus: u8,
  bats: [u8; 2],
  pits: [u8; 2],
}

impl Game {
  fn configure_cave(&mut self) {
    self.messages.push(
      "You've entered a clammy, dark cave, armed with 5 arrows.  You are very cold.".to_string(),
    );
    self.wumpus = js_rand(1, 20);
    self.bats[0] = self.get_empty_room();
    self.bats[1] = self.get_empty_room();
    self.pits[0] = self.get_empty_room();
    self.pits[1] = self.get_empty_room();
    self.warning_messages();
  }

  fn get_empty_room(&self) -> u8 {
    gen_range_avoiding(
      0,
      20,
      vec![
        self.current_room,
        self.wumpus,
        self.bats[0],
        self.bats[1],
        self.pits[0],
        self.pits[1],
      ],
    )
  }

  pub fn warning_messages(&mut self) {
    for adj in &room_exits(self.current_room).unwrap() {
      let t = *adj;
      if self.wumpus == t {
        self
          .messages
          .push("You smell something horrific and rancid.".into());
      } else if self.pits.contains(&t) {
        self
          .messages
          .push("You feel a cold updraft from a nearby cavern.".into());
      } else if self.bats.contains(&t) {
        self
          .messages
          .push("You hear a faint but distinct flapping of wings.".into());
      }
    }
  }
}

impl Default for Game {
  fn default() -> Self {
    let mut ret = Self {
      arrows: 5,
      current_room: 1,
      messages: Vec::new(),
      wumpus: 0,
      bats: [0, 0],
      pits: [0, 0],
    };
    ret.configure_cave();
    ret
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

将一切纳入`lib.rs` :
的范围

```
mod components;
mod game;
mod util;

use self::{
  components::{controls::Controls, messages::Messages, stats::Stats},
  game::Game,
  util::*,
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们还将“新游戏”设置移到了`Default`实现中。我们将不得不对`lib.rs`做一些改变。首先，我们要定义一些我们想要渲染的不同类型的`Model`。把你的`struct`改成这个`enum` :

```
pub enum Model {
  Waiting(String),
  Playing(Game),
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个游戏状态，当没有活动的游戏时。您可以删除旧的`impl Model`块——逻辑 ll 在`game.rs`中结束。当应用程序启动时，我们正在等待开始一个新的游戏:

```
impl Default for Model {
  fn default() -> Self {
    Model::Waiting("New Game!".into())
  }
}

impl Component for Model {
  // ..
  fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
    Model::default()
  }
  // .. 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个消息来开始一个新游戏:

```
#[derive(Debug, Clone)]
pub enum Msg {
  StartGame,
  SwitchRoom(u8),
} 
```

Enter fullscreen mode Exit fullscreen mode

这也需要对我们的`update`函数做一些修改。我们有一个新消息要处理，我们需要做一些额外的检查，以确保我们处于一个有意义的游戏状态:

```
fn update(&mut self, msg: Self::Message) -> ShouldRender {
  use self::Msg::*;
  match msg {
    SwitchRoom(target) => match self {
      Model::Playing(game) => {
        game.current_room = target;
        game.warning_messages();
      }
      _ => unreachable!(),
    },
    StartGame => *self = Model::Playing(Game::default()),
  }
  true
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在必须确保在交换房间之前我们正在玩游戏，但是我们可以在任何时候发送`StartGame`消息来重新注册游戏状态。

最后，我们在`view` :
中为每个游戏状态添加一个匹配手臂

```
impl Renderable<Model> for Model {
  fn view(&self) -> Html<Self> {
    use self::Model::*;

    match self {
      Waiting(s) => html! {
        <div class="hunt",>
          <span class="over-message",>{s}</span>
          <button onclick=|_| Msg::StartGame,>{"Play Again"}</button>
        </div>
      },
      Playing(game) => html! {
          <div class="hunt",>
              <div class="header",>{"Hunt the Wumpus"}</div>
              <div class="window",>
                <Stats: arrows=game.arrows, current_room=game.current_room,/>
                <Controls: exits=room_exits(game.current_room).unwrap(), onsignal=|msg| msg,/>
              </div>
              <Messages: messages=&game.messages,/>
          </div>
      },
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

每个状态都有自己的`html!`宏来渲染。为了更好的测量，在`hunt.scss` :
的最后一个右括号下面添加一点样式

```
.over-message {
  font-size: 22px;
  color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`game.rs`中，让我们在移动结束时充实我们想要检查的一切。在我们的`impl Game`块中添加一个新方法:

```
pub fn move_effects(&mut self) -> Option<String> {
  self.warning_messages();
  if self.current_room == self.wumpus {
    Some("You have been eaten slowly and painfully by the wumpus".into())
  } else if self.pits.contains(&self.current_room) {
    Some(
      "You have fallen into a bottomless pit and must now wait to die, falling all the while"
        .into(),
    )
  } else if self.bats.contains(&self.current_room) {
    // Switch us to a random room
    let current = self.current_room;
    let next = self.get_empty_room();
    self.messages.push(format!(
      "A gigantic bat whisks you from room {} to room {} before you can even blink",
      current, next
    ));
    self.current_room = next;
    self.warning_messages();
    None
  } else {
    None
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一些实际的行为！如果我们陷入万普斯或无底洞，我们会死。如果我们击中了一只蝙蝠，`current_room`将获得一个新的随机值，我们将获得新位置的一组新警告。

我让这个函数返回一个`Option<String>`。我们将用它来决定我们是否想要结束游戏——一个`None`将指示游戏应该继续，一个`Some(string)`将触发游戏的结束。

回到`lib.rs`，让我们调整我们的`update`函数。调整`SwitchRoom`消息处理器:

```
SwitchRoom(target) => match self {
       Model::Playing(game) => {
         game.current_room = target;
         if let Some(msg) = game.move_effects() {
           *self = Model::Waiting(msg);
         };
       }
       _ => unreachable!(),
     }, 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们可以在迷宫中漫步，并提前得到里面所有恐怖的警告。四处点击一会儿-你最终会死。那不是很有趣吗？

当然，还有最后一步——我们必须能够**射杀**这只该死的野兽。

首先，让我们为它创建消息。打开`lib.rs`并添加新的消息类型:

```
#[derive(Debug, Clone)]
pub enum Msg {
  StartGame,
  ShootArrow(u8),
  SwitchRoom(u8),
} 
```

Enter fullscreen mode Exit fullscreen mode

付款人出手时，我们需要处理几件事情。如果我们点击 wumpus，游戏将结束并显示一条胜利消息。如果我们错过了，这是我们的最后一支箭-我们运气不好-万普斯最终会找到你。那是立竿见影的损失。此外，我们并不一定很狡猾——每次我们射击时，都有 75%的可能会把温普斯吓到邻近的房间。如果隔壁房间碰巧有你，你就是温普斯的食物。这是 Rust 中的样子——在你的`update`函数中添加一个新的匹配臂:

```
 ShootArrow(target) => match self {
        Model::Playing(game) => {
          if game.wumpus == target {
            *self = Model::Waiting("With a sickening, satisfying thwack, your arrow finds its mark.  Wumpus for dinner tonight!  You win.".into());
          } else {
            game.arrows -= 1;
            game
              .messages
              .push("You arrow whistles aimlessly into the void".into());

            // If we exhausted our arrows, we lose
            if game.arrows == 0 {
              *self =
                Model::Waiting("You fired your very last arrow - you are now wumpus food".into());
            } else {
              // On each shot there's a 75% chance you scare the wumpus into an adjacent cell.
              let rand = js_rand(1, 4);
              if rand == 1 {
                game.messages.push(
                  "You listen quietly for any sign of movement - but the cave remains still."
                    .into(),
                );
              } else {
                game
                  .messages
                  .push("You hear a deafening roar - you've disturbed the wumpus!".into());
                let wumpus_exits = room_exits(game.wumpus).unwrap();
                let rand_idx = js_rand(0, 2);
                game.wumpus = wumpus_exits[rand_idx as usize];
                if game.wumpus == game.current_room {
                  *self = Model::Waiting(
                    "You scared the wumpus right on top of you.  Good going, mincemeat".into(),
                  );
                }
              }
            }
          }
        } 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们只需要一些按钮来发射箭。幸运的是，我们已经得到了几乎所有需要的东西。在`src/components/controls.rs`中，让我们对`move_button`的结尾:
做一点小小的调整

```
let move_button = |target: &u8| {
  use crate::Msg::*;
  let t = *target;
  html! {
      <div class="control-button",>
          <button onclick=|_| Msg::ButtonPressed(SwitchRoom(t)),>{&format!("Move to {}", target)}</button>
          <button onclick=|_| Msg::ButtonPressed(ShootArrow(t)),>{&format!("Shoot {}", target)}</button>
      </div>
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

新闻就是这样传播的！快乐的狩猎。下面是[第 3 部分](https://github.com/deciduously/hunt-the-wumpus/tree/master/part3)代码进行比较。

如果你改进这个应用程序，请告诉我！我想看看你能想出什么。