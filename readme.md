# 莉沫酱的实验室

大家好，我是莉沫酱，欢迎来到我的个人演唱会——啊，不是演唱会？

原来是实验室啊！<sub>(装作很惊讶的样子)</sub>


## 目录

1. 用2k显示器看1080p到底有没有比较清楚？

2. 还没有什么想法……

3. 会是什么呢？

4. 写什么好呢？


## 背景

最近家里的网络不太好，看1080P影片的时候常常会卡住，于是只好换到720P凑合一下。

因为我的2K显示器刚好是720P的两倍，就想起之前听过一个传言说「在2K显示器上看720P比看1080P的更清晰，因为整数倍缩放的效果更好」。

可是我没有感觉更清晰啊？我的眼睛明明是感觉变模糊了嘛？

——究竟是网上的人骗我还是真的有效呢？


## 设计

因为我也不记得传言说的是什么样的插值算法……所以想着，也许可以找一些2K的图片，然后分别用不同的插值算法缩小和放大。

我们得到重构图和原图的误差之后，就可以看看在各种情况下到底是1080P还是720P更清晰了。


## 代码

代码非常简单，只需要这样——

```python
import cv2
插值方法 = [
    'INTER_NEAREST',
    'INTER_LINEAR',
    'INTER_CUBIC',
    'INTER_AREA',
    'INTER_LANCZOS4',
]
def f(原图):
    for 目标尺寸 in [(1920, 1080), (1280, 720)]:
        for 插1 in 插值方法:
            for 插2 in 插值方法:
                小图 = cv2.resize(原图, 目标尺寸, interpolation=cv2.__getattribute__(插1))
                还原图 = cv2.resize(小图, (2560, 1440), interpolation=cv2.__getattribute__(插2))
                diff图 = 原图.astype(float) - 还原图.astype(float)
                yield (diff图**2).mean()**0.5, 目标尺寸[1], 插1, 插2
print(sorted(f(cv2.imread('./res/1.png'))))
```

## 结果

我挑了几张游戏CG作为输入，结果基本大同小异，以下是一个典型的输出。

| 重构损失<sub> (越小越清楚)</sub> | 多少P | 缩小插值算法 | 放大插值算法 |
| ---- | ---- | ---- | ---- |
| 4.1251773118807895 | 1080 | INTER_LANCZOS4 | INTER_LANCZOS4 |
| 4.26670313925519 | 1080 | INTER_CUBIC | INTER_LANCZOS4 |
| 4.408834805857602 | 1080 | INTER_LANCZOS4 | INTER_CUBIC |
| 4.592258828271043 | 1080 | INTER_CUBIC | INTER_CUBIC |
| 5.1562184430151055 | 1080 | INTER_LINEAR | INTER_LANCZOS4 |
| 5.408689647366871 | 1080 | INTER_AREA | INTER_LANCZOS4 |
| 5.55859471992847 | 1080 | INTER_LINEAR | INTER_CUBIC |
| 5.75176370646028 | 1080 | INTER_AREA | INTER_CUBIC |
| 6.038580187725733 | 1080 | INTER_LANCZOS4 | INTER_LINEAR |
| 6.093629621021338 | 1080 | INTER_CUBIC | INTER_LINEAR |
| 6.184521640727263 | 1080 | INTER_LANCZOS4 | INTER_AREA |
| 6.327657533031009 | 1080 | INTER_CUBIC | INTER_AREA |
| 7.171897177303725 | 1080 | INTER_LINEAR | INTER_AREA |
| 7.431593320574493 | 1080 | INTER_LINEAR | INTER_LINEAR |
| 7.644179354859438 | 1080 | INTER_AREA | INTER_AREA |
| 7.670306405277374 | 1080 | INTER_AREA | INTER_LINEAR |
| 9.023054866025515 | 720 | INTER_CUBIC | INTER_CUBIC |
| 9.023612687693328 | 720 | INTER_AREA | INTER_LANCZOS4 |
| 9.023612687693328 | 720 | INTER_LINEAR | INTER_LANCZOS4 |
| 9.13271950595021 | 720 | INTER_CUBIC | INTER_LANCZOS4 |
| 9.26323349840217 | 720 | INTER_AREA | INTER_CUBIC |
| 9.26323349840217 | 720 | INTER_LINEAR | INTER_CUBIC |
| 9.507876071601892 | 720 | INTER_LANCZOS4 | INTER_CUBIC |
| 9.736009988614873 | 720 | INTER_LANCZOS4 | INTER_LANCZOS4 |
| 10.097348855151688 | 720 | INTER_CUBIC | INTER_LINEAR |
| 10.464522700609068 | 720 | INTER_LANCZOS4 | INTER_LINEAR |
| 11.0395597070932 | 720 | INTER_AREA | INTER_LINEAR |
| 11.0395597070932 | 720 | INTER_LINEAR | INTER_LINEAR |
| 11.311554423373721 | 1080 | INTER_AREA | INTER_NEAREST |
| 11.675123554755551 | 1080 | INTER_LINEAR | INTER_NEAREST |
| 11.738486505069021 | 720 | INTER_AREA | INTER_AREA |
| 11.738486505069021 | 720 | INTER_AREA | INTER_NEAREST |
| 11.738486505069021 | 720 | INTER_LINEAR | INTER_AREA |
| 11.738486505069021 | 720 | INTER_LINEAR | INTER_NEAREST |
| 12.093264664213518 | 1080 | INTER_CUBIC | INTER_NEAREST |
| 12.220101431521444 | 1080 | INTER_LANCZOS4 | INTER_NEAREST |
| 12.345077020792765 | 720 | INTER_CUBIC | INTER_AREA |
| 12.345077020792765 | 720 | INTER_CUBIC | INTER_NEAREST |
| 12.512384518882866 | 720 | INTER_LANCZOS4 | INTER_AREA |
| 12.512384518882866 | 720 | INTER_LANCZOS4 | INTER_NEAREST |
| 13.08193517774766 | 1080 | INTER_NEAREST | INTER_LINEAR |
| 13.44592849093054 | 720 | INTER_NEAREST | INTER_LINEAR |
| 13.45428414533549 | 1080 | INTER_NEAREST | INTER_CUBIC |
| 13.63107543255178 | 1080 | INTER_NEAREST | INTER_LANCZOS4 |
| 13.667238903109302 | 720 | INTER_NEAREST | INTER_CUBIC |
| 13.942748694453472 | 720 | INTER_NEAREST | INTER_LANCZOS4 |
| 14.408056838794616 | 1080 | INTER_NEAREST | INTER_AREA |
| 16.476875883322702 | 720 | INTER_NEAREST | INTER_AREA |
| 16.476875883322702 | 720 | INTER_NEAREST | INTER_NEAREST |
| 19.734188771536335 | 1080 | INTER_NEAREST | INTER_NEAREST |

可以看出，如果选择一个最清楚的插值算法，1080P肯定是优于720P的，而且在大部分相同的插值算法下1080P也比720P更清楚——除了奇怪的`INTER_NEAREST`。

虽然不知道大家的播放器都用了什么插值算法，但一般来说不可能是`INTER_NEAREST`<sub>(不过也有不在上表里的可能)</sub>，因此我们可以得出结论，1080P实际上是比720P清楚的。

看来真的是网络上的人骗我，大家上网的时候一定要小心有害信息呀！

## 另一个实验

接下来我想验证一下，那「整数倍缩放的效果更好」，到底有好多少？

于是我又做了另外一个实验，同样是进行一次缩小和放大，然后看看不同大小下，重构误差有多大的差异。

这次的代码是这样——

```python
import cv2
import matplotlib.pyplot as plt

插值方法 = [
    'INTER_NEAREST',
    'INTER_LINEAR',
    'INTER_CUBIC',
    'INTER_AREA',
    'INTER_LANCZOS4',
]

def f(原图, 插):
    for y in range(100, 1440, 5):
        目标尺寸 = (int(y*16//9),y)
        小图 = cv2.resize(原图, 目标尺寸, interpolation=cv2.__getattribute__(插))
        还原图 = cv2.resize(小图, (2560, 1440), interpolation=cv2.__getattribute__(插))
        diff图 = 原图.astype(float) - 还原图.astype(float)
        yield (diff图**2).mean()**0.5, y

for 插 in 插值方法:
    rmse, y = zip(*(f(cv2.imread('bg.jpg'), 插)))
    plt.plot(y, rmse)
    plt.title(插)
    plt.xlabel('image height')
    plt.ylabel('RMSE')
    plt.ylim((0, 48))
    plt.savefig(f'{插}.png')
    plt.close()
```

结果是这样: 

![img/INTER_AREA.png](img/INTER_AREA.png)
![img/INTER_CUBIC.png](img/INTER_CUBIC.png)
![img/INTER_LANCZOS4.png](img/INTER_LANCZOS4.png)
![img/INTER_LINEAR.png](img/INTER_LINEAR.png)
![img/INTER_NEAREST.png](img/INTER_NEAREST.png)

因此我们可以得出结论，对于各种插值算法，除了奇怪的`INTER_NEAREST`，整数倍缩放在减少损失上要么没有任何效果，要么效果微乎其微。

也就是说，大家缩图的时候没有必要考虑整数倍缩放的问题。

## 结束

如果你喜欢莉沫酱的实验室，可以对你的萝莉朋友使用「送到莉沫酱实验室」互动，使她`+10%每月学识生活重心的经验值`。

最后祝各位鸡儿放假。
