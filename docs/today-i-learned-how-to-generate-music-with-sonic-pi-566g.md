# 今天我学会了:如何用音速圆周率生成音乐！

> 原文：<https://dev.to/sublimemarch/today-i-learned-how-to-generate-music-with-sonic-pi-566g>

Sonic Pi 是一个使用 Ruby 生成合成音乐的现场编码音乐程序。自从今年早些时候我了解了[algrave](https://en.wikipedia.org/wiki/Algorave)之后，我就对尝试用代码生成音乐非常感兴趣，Sonic Pi 似乎是一个很好的起点。

Sonic Pi 是免费和开源的，可用于 Windows、macOS 和 Raspberry Pi。它有一个神奇的内置教程，让我马上写音乐，我对教程的质量非常满意。我已经知道 Ruby 和一些中级音乐理论，但它不假设任何事先编程或音乐知识。

在大约两个小时左右的时间里，我完成了教程，并在我编码的时候将一些 rad synth 声音作为背景。[上 Soundcloud](https://soundcloud.com/wombatgal/sonic-pi-coding-loop-sample) 查一下，代码在下面！

```
live_loop :background do
  sample :loop_garzul
  use_synth :prophet
  play :c1, release: 4, cutoff: rrand(70, 130)
  sleep 4
  play :c2, release: 4, cutoff: rrand(70, 130)
end

live_loop :middle do
  sync :background
  use_random_seed 4923
  use_synth :tb303
  notes = (scale :c2, :minor_pentatonic, num_octaves: 1)
  sleep 4
  8.times do
    play notes.choose, release: 1, cutoff: rrand(30, 50), amp: 0.3
    sleep 0.5
  end

  sleep 4

  8.times do
    play notes.choose, release: 1, cutoff: rrand(30, 50), amp: 0.3
    sleep 0.5
  end
end 
```

有一个相当活跃的 [Sonic Pi Twitter 账户](https://twitter.com/sonic_pi)分享最近的创作(其中一些可以放在一条推文中！)以及[一个我没怎么探索过的论坛](https://in-thread.sonic-pi.net/)。有没有用 Sonic Pi 或者别的什么生成音乐的？请在评论中告诉我！