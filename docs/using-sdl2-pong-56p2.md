# 使用 SDL2: Pong

> 原文：<https://dev.to/noah11012/using-sdl2-pong-56p2>

我们将直接用 SDL2 和 C++制作一个 Pong 克隆。我已经创建了三个文件:main.cpp、pong.hpp 和 pong.cpp，Main.cpp 只有一个工作:创建一个`Pong`类的实例并调用方法`game_loop()`。

main.cpp:

```
#include "pong.hpp" 
int main()           
{                    
    Pong pong;       
    pong.game_loop();
} 
```

Enter fullscreen mode Exit fullscreen mode

驻留在 pong.cpp 中的`Pong`类处理我们的窗口、渲染器、窗口事件，并最终在本文中处理更多内容。

pong.hpp:

```
#pragma once 
#include <SDL2/SDL.h> 
class Pong                                  
{                                           
public:                                     
    Pong();                                 
    ~Pong() = default;                      

    void game_loop();                       
    void update(double delta_time);         
    void draw();                            

private:                                    
    SDL_Window   *m_game_window;            
    SDL_Event     m_game_window_event;      
    SDL_Renderer *m_game_window_renderer;   
}; 
```

Enter fullscreen mode Exit fullscreen mode

pong.cpp:

```
#include "pong.hpp" 
Pong::Pong()                                                   
{                                                              
    SDL_CreateWindowAndRenderer(680, 480, SDL_WINDOW_RESIZABLE,
 &m_game_window, &m_game_window_renderer);                     
}                                                              

void Pong::game_loop()                                         
{                                                              
    bool keep_running = true;                                  
    while(keep_running)                                        
    {                                                          
        while(SDL_PollEvent(&m_game_window_event) > 0)         
        {                                                      
            switch(m_game_window_event.type)                   
            {                                                  
                case SDL_QUIT:                                 
                    keep_running = false;                      
            }                                                  
        }                                                      

        update(1.0/60.0);                                      
        draw();                                                
    }                                                          
}                                                              

void Pong::update(double delta_time)                           
{                                                              

}                                             

void Pong::draw()                             
{                                             
    SDL_RenderClear(m_game_window_renderer);  

    SDL_RenderPresent(m_game_window_renderer);
} 
```

Enter fullscreen mode Exit fullscreen mode

在构造函数中，我们使用`SDL_CreateWindowAndRenderer()`初始化窗口和渲染器。这是创建窗口，然后从窗口创建渲染器的快捷方式。我们还允许调整窗口的大小，以防用户对默认尺寸不满意。这有一个我们必须谈论的含义。如果我们允许调整窗口的大小，这意味着我们在游戏中的对象将改变尺寸/形状。我们需要一个独立于窗口尺寸的分辨率。由于 SDL2 是一个相当低级的库，这很容易实现。在构造函数中，添加函数`SDL_RenderSetLogicalSize()`，它接受一个指向`SDL_Renderer`的指针和两个宽度和高度参数。

```
SDL_RenderSetLogicalSize(m_game_window_renderer, 400, 400); 
```

Enter fullscreen mode Exit fullscreen mode

下一步是制造桨。像往常一样，我们将创建一个类来管理位置、更新和对屏幕的绘制。

paddle.hpp:

```
#pragma once 
#include <SDL2/SDL.h>                    
#include "paddle.hpp" 
class Pong                               
{                                        
public:                                  
    Pong();                              
    ~Pong() = default;                   

    void game_loop();                    
    void update(double delta_time);      
    void draw();                         

private:                                 
    SDL_Window   *m_game_window;         
    SDL_Event     m_game_window_event;   
    SDL_Renderer *m_game_window_renderer;

    Paddle        m_left_paddle;         
    Paddle        m_right_paddle;        
}; 
```

Enter fullscreen mode Exit fullscreen mode

paddle.cpp:

```
#include "paddle.hpp" 
Paddle::Paddle(int x, int y)                     
{                                                
    m_position.x = x;                            
    m_position.y = y;                            
    m_position.w = 50;                           
    m_position.h = 100;                          
}                                                

void Paddle::handle_input(SDL_Event const &event)
{                                                

}                                                

void Paddle::update(double delta_time)           
{                                                

}                                                

void Paddle::draw()                              
{                                                

} 
```

Enter fullscreen mode Exit fullscreen mode

我们还将`Paddle`类添加到`Pong`类的私有部分，并在构造函数初始化列表中初始化它们:

pong.hpp:

```
#include "paddle.hpp" 
...

Paddle        m_left_paddle; 
Paddle        m_right_paddle; 
```

Enter fullscreen mode Exit fullscreen mode

pong.cpp:

```
Pong::Pong(): m_left_paddle(0, (400 / 2) - 50), m_right_paddle(400 - 50, (400 / 2) - 50)
{
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在`Pong`的游戏循环中，任何不是由`Pong`本身处理的事件都要像拨片一样发送给游戏中的其他对象。

```
while(SDL_PollEvent(&m_game_window_event) > 0)       
{                                                    
    switch(m_game_window_event.type)                 
    {                                                
        ...                   
    }                                                

    m_left_paddle.handle_input(m_game_window_event); 
    m_right_paddle.handle_input(m_game_window_event);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

在下一部分中，我们将让左右拨片在屏幕上上下移动。

像往常一样，本系列的源代码可以在我的 Github 存储库中找到:

[https://github.com/Noah11012/sdl2-tutorial-code](https://github.com/Noah11012/sdl2-tutorial-code)