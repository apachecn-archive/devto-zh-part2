# 动态更新 Jupyter 笔记本中的 Matplotlib 图形

> 原文：<https://dev.to/seanlane/dynamically-updating-matplotlib-figures-in-jupyter-notebooks-2ie8>

动态更新 matplotlib 数字似乎有点麻烦，但下面的代码似乎可以解决这个问题。这是一个输出具有多个子情节的图形的示例，每个子情节具有多个情节。奇怪的是，在写入时，图像将小于数字，直到 Jupyter 单元停止运行，但这可以修复，但在一个单元中生成数字，然后在随后的单元 <sup id="fnref:1">[1](#fn:1)</sup> 中更新图像。

运行该代码时，假设可以在名为`data.txt`的工作目录中找到以下数据文件:

[样本 TCL 数据](https://sean.lane.sh/files/2018/sample-tcl-data.txt)

```
import numpy as np
import time
import matplotlib.pyplot as plt

%matplotlib notebook

def load_data():
    data = np.genfromtxt('data.txt', delimiter=',', skip_header=1)
    tm = data[:, 0]
    Q1 = data[:, 1]
    Q2 = data[:, 2]
    T1 = data[:, 3]
    T2 = data[:, 4]
    return (tm, Q1, Q2, T1, T2) 

(m_time, Q1s, Q2s, T1s, T2s) = load_data()

n = len(m_time)

labels = [
    [r'$T_1$ measured', r'$T_2$ measured'],
    # [r'$T_1 set point$', r'$T_2 set point$'],
    [r'$Q_1$', r'$Q_2$']
]

colors = [
    ['r:', 'b-'],
    ['r:', 'bx']
]

def plot_init(num_subplots=1, x_labels=None, y_labels=None):
    if not x_labels:
        x_labels = [None] * num_subplots
    if not y_labels:
        y_labels = [None] * num_subplots
    fig = plt.figure(figsize=(12,6), dpi=80)
    fig.subplots_adjust(hspace=.5)
    axes = []
    for i in range(1, num_subplots + 1):
        ax = plt.subplot(num_subplots, 1, i)
        ax.grid()
        if x_labels[i-1]:
            ax.set_xlabel(x_labels[i-1]) 
        if y_labels[i-1]:
            ax.set_ylabel(y_labels[i-1]) 
        axes.append(ax)
    return fig, axes

def plot_update(fig, axes, xs, ys, colors, labels):
    for i in range(len(axes)):
        for j in range(len(ys[i])):
            axes[i].plot(xs, ys[i][j], colors[i][j])
        axes[i].legend(labels=labels[i])
    fig.canvas.draw()

fig, axes = plot_init(
    num_subplots=2, 
    x_labels=[None, 'Time (sec)'],
    y_labels=['Temps (C)', 'Heaters']
)

for i in range(1, n):
    try:
        ys = [
            [T1s[:i], T2s[:i]],
            [Q1s[:i], Q2s[:i]]
        ]
        plot_update(fig, axes, m_time[:i], ys, colors, labels)
        time.sleep(0.2)
    except KeyboardInterrupt:
        break 
```

* * *

1.  [堆栈溢出:Jupyter 笔记本 matplotlib 数字显示很小，直到单元格完成](https://stackoverflow.com/questions/45384072/jupyter-notebook-matplotlib-figures-show-up-small-until-cell-is-completed) <sup>【返回】</sup>