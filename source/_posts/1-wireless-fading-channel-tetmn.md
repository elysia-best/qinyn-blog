---
title: 无线衰落信道
date: '2025-08-25 10:51:18'
updated: '2025-12-19 10:31:42'
tags:
  - 通信学习
permalink: /post/1-wireless-fading-channel-tetmn.html
comments: true
toc: true
---



众所周知， 无线信道环境在很大程度上决定了一个无线通信系统的性能。相比于性质相对稳定的有线信道，无线信道的动态特性信道特征的准确预测和精准分析带来了不少的难度。由此可见，克服无线信道的影响是无线通信技术中最关键的问题之一。

在无线通信中，无线信号的传播定义为电磁波从发射端传播到接收端的过程。在该过程中，电磁波会受到3种物理现象的影响，分别为：反射、绕射和散射 (i.e. reflection, diffraction, and scattering) [1]。

- 反射： 反射是电磁波在传播过程中遇到了比自己波长大很多（即远大于）的障碍物发生的现象。部分电磁波（体现为信号功率）会返回到发射端，而不是完全沿着传输路径抵达发射端。

  ![image](/images/siyuan-note-publish/image-20250825111335-567l8fs.png "反射波和直射波的区别 [2]")
- 绕射：也称之为衍射。当电磁波的传输路径被一些尖锐、不规整的物体表面或者小的缺口时发生的物理现象。电磁波可以经过这些表面或者孔缝后继续扩散。（理解上可以参考光的衍射，下面的配图意思为，经过绕射后传播方向可以发生变化）。这使得，即使不存在可视的传播路径，电磁波仍然可以用衍射的方式到达接收端。

  ![image](/images/siyuan-note-publish/image-20250825112830-88oto74.png "电磁波经过尖锐表面发生绕射  [2]")
- 散射：由一个或者多个尺寸远小于电磁波波长的障碍物引起的电磁波偏离其原来传播方向的物理现象。常见的引起散射的障碍物包含：树木，路标，灯柱等。
- ![image](/images/siyuan-note-publish/image-20250825113134-aizcl0e.png "电磁波经过树木发生散射 [2]")

经由这些物理现象的综合影响，不同位置处的信号强度也是不同的。

## 1.1 衰落信道分类

反射、绕射和散射会引起电磁波幅值和相位上的变化。这种变化就称为无线信道的衰落现象 (Fading)。衰落和加性噪声(addtive noise) 都是信号恶化的来源。但是和加性噪声不同的是，衰落对无线信道带来的影响并不是加性的。除了上面的几种物理现象，衰落还可以由多径传播引起（多径衰落，multipath fading），也可由障碍物的遮蔽引起（称之为阴影衰落，shadowing）。

> 多径：就是电磁波有多种传播路径啦~

根据衰落的程度而言，衰落可以分为大尺度衰落和小尺度衰落 (i.e. Large scale fading and small scale fading)。大尺度衰落主要由FSPL和Sadowing来体现，一般在比较长的距离上表现更为明显。小尺度衰落一般是由Multipath effect所引起的不同电磁波在相位和幅值上的叠加导致的。由于不同路径下电磁波传递所需时间不同，不同路径的时延就会导致叠加后的电磁波电平快速变动（不同电磁波之间发生干涉）。这种分类方式的图示如下。

![image](/images/siyuan-note-publish/image-20250825143307-ajfbgvu.png "衰落信道的分类 [1]")

此外，由于我们在通信过程中另一个重要的关心点为：的**时间选择性信道**和**频率选择性信道**。因此，可以将实际的信道看作一个线性时变系统（Linear Time Varing System，LTV），记为 $h\left(f,t\right)$。基于此，又可以对衰落信道做如下的分类了。

![image](/images/siyuan-note-publish/image-20250825143222-7np70pj.png "基于LTV进行理解的衰落信道分类 [3]")

## 1.2 大尺度衰落

First, let's start with the Free Space Path Loss (FSPL) channel model. The FSPL is a simple channel model that is used to model the loss of signal power due to distance between the transmitter and the receiver. The FSPL is given by:

$$
P_r(d) = \frac{P_tG_tG_r\lambda^2}{(4\pi)^2d^2L} \tag{1}
$$

where $L$ should be the loss factor of the system. If the Rx and Tx systems are ideal, $L$ is equal to 1. If there are losses in the system, $L > 1$.

Considering a ideal system, the FSPL can be expressed in dB as follows:

$$
\mathrm{PL} _ F(d)[dB] = 10 \log _ {10}\left( \frac{P_t}{P_r} \right) = -10\log_{10} \left( \frac{G_tG_r\lambda^2}{(4\pi)^2d^2} \right) \tag{2}
$$

The following code simulats the FSPL when Gains are all equal to 1 or 0.5.

```Python
# Import some libs
import numpy as np
# For plotting
%matplotlib inline
# also try %matplotlib widget
import matplotlib.pyplot as plt

Gt = [1, 1, 0.5] # Tx gains
Gr = [1, 0.5, 0.5] # Rx gains
fc = 1.5e9 # Carrier Frequency in Hz
lambda_c = 3e8/fc # Wavelength in meters
distance = np.arange(1, 32, 2)**2 # Distance in meters
PL_F = np.zeros((len(Gt), len(distance)))
for i in range(len(Gt)):
    # FSPL in dB
    PL_F[i, :] = -10*np.log10( (Gt[i]*Gr[i]*lambda_c**2) / (4*np.pi*distance)**2 )

# Plot the FSPL on logscale
fig = plt.figure()
fig.set_dpi(600)
plt.semilogx(distance, PL_F[0, :], label='Gt=1, Gr=1') 
plt.semilogx(distance, PL_F[1, :], label='Gt=1, Gr=0.5')
plt.semilogx(distance, PL_F[2, :], label='Gt=0.5, Gr=0.5')
plt.xlabel('Distance [m]')
plt.ylabel('Path Loss [dB]')
plt.legend()
plt.grid()
plt.show()
```

输出如下：

![image](/images/siyuan-note-publish/image-20250825151915-yc45dzl.png "自由空间损耗模型")

与[1]中的结果进行对比，发现图形高度吻合，证明了此代码的正确性。从图中可以直接看出，路径损耗随着发送接收端距离的增长成对数形增长（图中使用了semilogx进行绘图）。同时，增大发射和接收天线的增益可以改善路径损耗的大小。根据公式（2）不难看出，当载波频率增加的时候，在相同间距下的路径损耗会随之增加，表明高频载波衰减更快。

## 1.3 小尺度衰落

小尺寸衰落是指在短期内信道发生的波动/衰落。当移动台发生移动时，接收信号会在短时期内快速波动。小尺寸衰落主要由一下因素决定：多径传播、移动台的速度、周围物体的速度和信号的传输带宽 [1]。

### 1.3.1 小尺度衰落的参数

小尺度衰落通常使用**功率时延分布（Power Delay Profile, PDP）** 进行描述。下表是一个ITU Pedestrian A 模型给出的PDP例子。

|Path Index|Avg. Delay (ns)|Avg. Power (dB)|
| ------------| -----------------| -----------------|
|1|0|0.0|
|2|110|-9.7|
|3|190|-19.2|
|4|410|-22.8|

一般描述多径时延扩展的参数有平均时延扩展（Average Excess Delay）$\bar{\tau}$ 和 均方根时延扩展（RMS Delay Spread）$\sigma_\tau$。

令$\tau_k$、$a_k$ 和 $P(\tau_k)$ 分别代表第$k$条路径的信道时延、幅度和功率，则上述两个参数的定义如下 [1]。

平均时延扩展 $\bar{\tau}$ 定义为：

$$
\bar{\tau} = \frac{\sum_k{a_k^2\tau_k}}{\sum_ka_k^2} = \frac{\sum_k\tau_k P(t_k)}{\sum_k P(t_k)} \tag{3}
$$

RMS时延扩展$\sigma_\tau$则由PDP的二阶中心距的平方根给出

$$
\sigma_\tau = \sqrt{\bar{\tau^2} - (\bar{\tau})^2} \tag{4}
$$

>  二阶中心矩是描述随机变量相对于其均值的离散程度的统计量，通常用方差表示。所以这里的RMS时延扩展类似于标准差，符号就是$\sigma$

显然，$\bar{\tau^2}$ 就表示为

$$
\bar{\tau^2} = \frac{\sum_k{a_k^2\tau_k^2}}{\sum_ka_k^2} = \frac{\sum_k\tau_k^2 P(t_k)}{\sum_k P(t_k)} \tag{5}
$$

一般将均方根时延扩展$\sigma_\tau$看作该多径信道的时延扩展。$\sigma_\tau$越大时延扩展越严重，反之$\sigma_\tau$越小时延扩展越轻微。

同时，在时域上看，时延扩展会导致频率选择性衰落，即信号通过多径信道后某些频率成分的幅值被增强而另外一些频率成分的幅值被削弱。为此可以定义一个相干带宽 $B_c$，其与RMS时延扩展成反比关系，即

$$
B_c \propto \frac{1}{\sigma_\tau} \tag{6}
$$

根据定义不同，式(6)的比例也不相同。比如，当相干带宽定义为相关函数大于等于0.9时对应的带宽，此时的关系为

$$
B_c \approx \frac{1}{5\sigma_\tau} \tag{7}
$$

### 1.3.2 频率选择性衰落

从频域上看，发射信号会在频域经历一个选择性衰落或者一个非选择性衰落（平坦衰落）。是否经历频率选择性衰落，和信道的时延扩展以及信号的符号周期相关。

![由多径信道的时延扩展引起的衰落特性 [1\]](assets/image-20250827111240-0ekgaz5.png "由多径信道的时延扩展引起的衰落特性 [1]")

如图(a)所示，当符号周期 $T$ 远大于信道时延 $\tau$ 的时候，当前传输的符号就不会对下一符号产生很大的影响，表面了符号间干扰（Inter Symbold Interference，ISI）并不明显。同样的，从频域图中也可以了解到，如果信号的带宽 $B_s$ 比信道的带宽小很多，信道也不会对频域造成很严重的影响。因此，当 $B_s \ll B_c$ 并且 $T \gg \sigma_t$ 时，信号经历非频率选择性衰落（即平坦衰落）。

但是当信道时延 $\tau$ 大于了一个符号周期 $T$ 的时候，发射信号的多个时延分量将会和下一个符号重叠，如图(b)，从而产生ISI。观察图形可知，此时的信号带宽大于了信道的带宽，且符号周期小于信道的时延，即  $B_s \gt B_c$ 并且 $T \lt \sigma_t$ 。

### 1.3.3 时间选择性衰落（多普勒扩展）

根据多普勒扩展程度，可分为快衰落和慢衰落。下面主要分析快衰落，此时相干时间小于符号的周期，在一个周期内信道脉冲会发生快速变化。

>  当接收机和发送机之前存在相对运动的时候，会引起信号在频域的扩展，称为多普勒频移（Doppler Shift）。

令 $f_m$ 表示最大多普勒频移，$B_d$ 为多普勒带宽，则满足 $B_d = 2f_m$。相干时间 $T_c$ 则与多普勒频谱扩展 $B_d$ 成反比，即

$$
T_c \propto \frac{1}{B_d} \tag{8}
$$

相干时间小于符号的周期，写成表达式为 $T_c \lt T$，那么 $B_d > B_s$。此时发射信号经历**快衰落**。

类似的，当 $T_c \gg T$ 且 $B_d \ll Bs$ 时，发射信号经历**慢衰落**。

公式(8)是在信号变化十分缓慢的情况下得出的。在<u>信号快速变化的假设下</u>，当相关函数大于等于0.5时，相干时间 $T_c$ 的表示从式(8)变为，

$$
T_c \approx \frac{9}{16\pi f_m} \tag{9}
$$

对公式(8)和(9)求几何平均（i.e. $\sqrt{ab}$），得到常见的**相干时间** $T_c$ 的**表达式**为，

$$
T_c = \sqrt{\frac{9}{16\pi f_m^2}} \approx \frac{0.423}{f_m} \tag{10}
$$

**注意**⚠️：无线信道的**频率选择性和快/慢衰落并无直接的联系**。快衰落是由运动的终端带来的信道变化，而频率选择性信道则是由信道以及信号本身的特性来判断的（见上一小节）。

### 1.3.4 衰落信道的统计特性

Clarke 基于理论推导给出了衰落信道的统计模型，即在移动台通过一个散射环境时，可以用统计的方法来描述移动台接收型号的电磁场特点 [4]。在这个模型中，假设共有 $N$ 个任意相位的平面波，以任意方向到达移动台，且所有波的功率相同。

![Planewave arriving at the receiver that moves in the direction of x with a velocity of v [1\].](assets/image-20250827111151-p94fakv.png "Planewave arriving at the receiver that moves in the direction of x with a velocity of v [1].")

现在考虑移动台的模型，如上图所示，一个平面波以角度 $\theta$ **到达**一个速度为 $v$ 的移动台。显然，发射信号将会受到多普勒频移和信道时延的影响。假设基带信号 (i.e. baseband signal) 为 $x(t)$，那么通频带 (passband) 上的发射信号为,

$$
\tilde{x}(t) = \Re{\left[ x(t)e^{j2\pi f_c t} \right]} \tag{11}
$$

> 这里利用了傅里叶变换的频移特性，即 $\mathscr{F}\left[ z(t)e^{j\omega_0t} \right] = Z(\omega + \omega_0)$
>
> 关于时延 $t-\tau_i$：可以这样想，要听到某个声音的延迟后播发的版本，就等价于去听原始声音在 $\tau_i$ 秒之前的情况（因为原本的声音早已收到了）。在当前时间 t，听到的延迟版本的声音是原始信号中  $t-\tau_i$ 时刻发生的情况。

当发射信号通过了 一个拥有 $I$ 条不同传播路径的散射信道后，抵达移动台的信号可以表示为，

$$
\tilde{y}(t) = \Re \left[ \sum_{i=1}^{I} C_ie^{j2\pi(f_c + f_i)(t-\tau_i)}x(t- \tau_i) \right] \tag{12}
$$

$C_i$、$\tau_i$ 和 $f_i$ 分别代表了第 $i$ 条路径上的信道增益（channel gain）、时延和多普勒频移。它们可以用移动台的速度 $v$ 和信号的波长 $\lambda$ 求得。其中 $f_m$ 是最大多普勒频移，$\theta_i$ 是第 $i$ 个平面波的到达角（Angle of Arraival, abbr. AoA）。

$$
f_i = f_m \cos \theta_i = \frac{v}{\lambda} \cos \theta_i \tag{13}
$$

> 关于上方多普勒频移的说明（以防忘记）：从图中可知，移动台的速度是 $v$，利用三角关系换算后，其在平面波的传播方向上以 $v\cos \theta$ 的速度靠近波源。以移动台为参考点，移动台看到的波速就成为了 $c + v\cos \theta$，新的频率则为 $f' = \frac{c + v \cos \theta}{\lambda} = f + \Delta f$。因此，多普勒频移的表达式就为 $v \cos \theta / \lambda$。当 $\theta = 0$ 时出现极值，即移动台运动方向和波的传播方向在同一方向的时候。

进一步从式(12)中将接收基带信号 $y(t)$ 和载波$e^{j2\pi f_c t}$分开，得到

$$
\begin{align}
	\tilde{y}(t) &= \Re \left[ \sum_{i=1}^{I} C_i e^{j2\pi(f_c + f_i)(t-\tau_i)}x(t- \tau_i) \right] \\
	&= \Re \left[ \sum_{i=1}^{I} C_i e^{2\pi f_c t} e^{-j2\pi \left[ (f_c + f_i) \tau_i - f_i t \right]}x(t- \tau_i) \right] \\
	&= \Re \left[ y(t) e^{j2\pi f_c t} \right]

\end{align}
\tag{14}
$$

因此，接收的基带信号表示为，

$$
y(t) = \sum_{i=1}^I C_i e^{-j\phi_i (t)}x(t-\tau_i) \tag{15}
$$

其中 $\phi_i (t) = 2\pi \left[ (f_c + f_i ) \tau_i - f_i t \right]$。式(15)可以看作一个线性**时变**滤波器，那么信道自然地可以作为这个滤波器的冲击响应得到（注意这里不是是不变的欧~）

$$
h(t,\tau) = \sum_{i=1}^I C_i e^{-j\phi_i (t)}\delta(t-\tau_i) \tag{16}
$$

其中 $\delta(\cdot)$ 是狄拉克δ函数。

如果不同路径的时延之间的区别比采样周期 $T_s$ 小很多，那么这些不同的路径时延可以用 $\hat{\tau}$ 来近似代替 [1] （注：此处中文版翻译有误，原文为 difference in the path delay）。如此一来，式(16)就可以简化为

$$
h(t,\tau) = h(t)\delta(t-\hat{\tau}) \tag{17}
$$

其中 $h(t) = \sum_{i=1}^I C_i e^{-j\phi_i (t)}$。假设发送信号 $x(t) = 1$，由式(14)和欧拉恒等式可得，接收端的信号可以表示为

$$
\begin{align}
	\tilde{y}(t) &= \Re \left[ y(t)e^{2\pi f_c t} \right] \\
	&= \Re \{ \left[ h_I(t) + jh_Q(t) \right] e^{j2\pi f_c t} \} \\ 
	&= h_I(t)\cos 2\pi f_c t - h_Q(t)\sin 2\pi f_c t
\end{align}
\tag{18}
$$

其中，$h_I(t)$ 和 $h_Q(t)$ 是 $h(t)$ 的同相且正交的分量（称之为 I/Q signal，In-Phase and Quadrature）,

$$
h_I(t) = \sum_{i=i}^I C_i \cos \phi_i (t) \tag{19}
$$

$$
h_Q(t) = \sum_{i=i}^I C_i \sin \phi_i (t) \tag{20}
$$

根据中心极限定理可知（参考这里），当 $I$ 足够大时，式(19)和(20)中的 $h_I(t)$ 和 $h_Q(t)$ 可视为高斯随机变量。因此，接收信号的 $\tilde{y}$ 的**幅度** $\sqrt{h_I^2(t) + h_Q^2(t)}$ 服从瑞利分布（参见瑞利分布 Rayleigh Distribution），此衰落称为**瑞利衰落**。

（此处我没有去计算了）衰落过程的功率谱密度 PSD 就可以从 $\tilde{y}(t)$ 的自相关函数计算得到 [1]，

$$
S_{\tilde{y} \tilde{y}}(f)=\left\{\begin{array}{ll}
\displaystyle \frac{\Omega_{p}}{4 \pi f_{m}} \frac{1}{\sqrt{1-\left(\frac{f-f_{c}}{f_{m}}\right)^{2}}} & \left|f-f_{c}\right| \leq f_{m} \\ \\
\displaystyle  0 & \text { otherwise }
\end{array}\right.

\tag{21}
$$

其中，$\Omega_p = E\{h ^2_I(t) \} + E\{ h_Q^2(t) \} = \sum_{i=1}^I C_i^2$。式(21)的PSD也被称为***经典多普勒谱***。

当一部分路径的功率明显强于其他路径的功率时，此时衰落的过程不再是瑞利衰落了。此时接收信号的 $\tilde{y}$ 的**幅度（模）** 服从**莱斯分布**，称为**莱斯衰落**。通常，最强的散射分量对应于**LOS分量（Line-of-sight）** （也称为 *specular* components，意为镜面（反射）分量）。其他的分量则视为NLOS分量（Non-LOS）（也叫做 *scattering* components）。令 $\tilde{p}(\theta)$ 表示NLOS分量的PDF（这些分量服从瑞利分布），$\theta_0$ 表示LOS分量的AoA，则所有分量AoA总和的PDF为：

$$
p(\theta) = \frac{1}{1+K}\tilde{p}(\theta) + \frac{K}{1+K} \delta(\theta - \theta_0) \tag{21}
$$

其中，$K$ 是莱斯因子（Rician factor），定义为LOS分量功率 $c^2$ 和NLOS分量功率 $2\sigma^2$ 的比值

$$
K = \frac{c^2}{2\sigma^2} \tag{22}
$$

NLOS与LOS环境的示意图如下 [1]。图中“视线”即为 Line-of-sight。

![image](/images/siyuan-note-publish/image-20250828084539-386cu37.png)

### 1.3.5 衰落信道生成与仿真

从 1.3.4 节可知，在瑞利衰落中，接收信号可以用一个复高斯随机变量来表示，$W_1 + jW_2$，其中$W_1 \sim{\mathcal{N}(0,\sigma^2)}$且$W_2 \sim{\mathcal{N}(0,\sigma^2)}$，即 $W_1$ 和 $W_2$ 独立同分布（i.i.d）。令 $X$ 代表这个复高斯随机变量的模，即 $X = \sqrt{W_1^2 + W_2^2}$。那么，这个随机变量 $X$ 就服从瑞利分布了，是瑞利随机变量。

生成一个瑞利衰落的代码如下 [5]：

```python
def complex_normal(shape, var=1.0, precision=None):
    r"""Generates a tensor of complex normal random variables

    Input
    -----
    shape : `tf.shape`, or `list`
        Desired shape

    var : `float`
        Total variance., i.e., each complex dimension has
        variance ``var/2``.

    precision : `None` (default) | "single" | "double"
        Precision used for internal calculations and outputs.
        If set to `None`,
        :attr:`~sionna.phy.config.Config.precision` is used.

    Output
    ------
    : ``shape``, `tf.complex`
        Tensor of complex normal random variables
    """
    if precision is None:
        precision = config.precision
    rdtype = dtypes[precision]['tf']['rdtype']

    # Half the variance for each dimension
    var_dim = tf.cast(var, rdtype)/tf.cast(2, rdtype)
    stddev = tf.sqrt(var_dim)

    # Generate complex Gaussian noise with the right variance
    xr = config.tf_rng.normal(shape, stddev=stddev, dtype=rdtype)
    xi = config.tf_rng.normal(shape, stddev=stddev, dtype=rdtype)
    x = tf.complex(xr, xi)

    return x
```

仿真代码如下：

```python
from sionna.phy.utils.misc import complex_normal
# For plotting
import tensorflow as tf
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

N = int(2000000) # Number of samples to generate
H = complex_normal((N, 1)) # Generate a complex Gaussian random variable
# Plot the histogram of the norm
counts, bins = np.histogram(tf.math.abs(H).numpy(), bins=30)
plt.plot(bins[:-1], counts, marker='^', color='coral')
# 获取当前轴对象
ax = plt.gca()
# 设置 y 轴为科学计数法
ax.ticklabel_format(style='sci', scilimits=(0, 2.5), axis='y')
plt.xlabel('Magnitude')
plt.ylabel('Counts')
plt.grid()
plt.show()
```

使用 Python 运行后，输出结果如下图所示。与参考文献[1]中的仿真结果匹配，证明理论的正确性。

![image](/images/siyuan-note-publish/image-20250828102040-xe5wsvl.png)

同时，使用 `np.var(tf.math.abs(H).numpy()**2)`​计算实际生成的信道幅值的方差，结果为`0.9980489`​，十分接近输入的默认值 `1`，表明归一化的代码起到了作用。

接下来仿真莱斯衰落。在 1.3.4 衰落信道的统计特性 节提到，莱斯衰落环境由LOS分量和NLOS分量构成。对于LOS分量而言，因其不存在散射、反射等现象，到达接收机的幅度可以用 $c$ 直接表示。再叠加上NLOS分量，最后莱斯衰落中接收幅度可表示为 $c + W_1^2 + jW_2^2$。这个主信号（正弦（余弦）信号）加上高斯随机信号的**包络**服从莱斯分布。

对莱斯信道仿真，编写的程序如下。

```python
# 莱斯信道仿真

def ric_model(K_db, N):
    """Rician model generator

    Args:
        K_db (float): Rician factor
        N (int): number of samples to generate
    """
    K = 10**(K_db/10)
    H_ray = complex_normal((N, 1))
    H = tf.complex(tf.math.sqrt(K/(K+1)), 0.0) + tf.complex(tf.math.sqrt(1/(K+1)), 0.0)*H_ray
    return H


K_dB = [-40, 15] # Rician factor in dB
colours = ['black', 'blue']

for k, colour_ in zip(K_dB, colours):
    H = ric_model(k, N) # Generate a complex Gaussian random variable
    # Plot the histogram of the real part
    counts, bins = np.histogram(tf.math.abs(H).numpy(), bins=30)
    plt.plot(bins[:-1], counts, marker='^', color=colour_)

# Also plot Rayleigh channel
H = complex_normal((N, 1)) # Generate a complex Gaussian random variable
# Plot the histogram of the real part
counts, bins = np.histogram(tf.math.abs(H).numpy(), bins=30)
plt.plot(bins[:-1], counts, marker='o', color='coral')

# 获取当前轴对象
ax = plt.gca()
# 设置 y 轴为科学计数法
ax.ticklabel_format(style='sci', scilimits=(0, 2.5), axis='y')
ax.legend(['K=-40 dB', 'K=15 dB', 'Rayleigh'])
plt.xlabel('Magnitude')
plt.ylabel('Counts')
plt.grid()
plt.show()
```

 输出结果如下：

![瑞利衰落和莱斯衰落的信道分布](/images/siyuan-note-publish/image-20250829085608-5mlaaxu.png "瑞利衰落和莱斯衰落的信道分布")

可以看到，当 $K$ 较小的时候，式(21)中相当于只存在NLOS分量，此刻的PDF形状类似瑞利分布（上图橙色和黑色）。当 $K$较大的时候，式(21)趋于正态分布的PDF.　对于瑞利信道而言，$K \approx -40 \, \mathrm{dB}$，对于高斯信道而言，$K \gt 15 \, \mathrm{dB}$。

## References

- [1] Y. S. Cho, J. Kim, W. Y. Yang, and C. G. Kang, *MIMO-OFDM Wireless Communications with MATLAB*. Wiley-IEEE Press, 2010.
- [2] “无线信道之电磁波传播机制(一)\_电磁波在空间传播方式-CSDN博客,” *CSDN*, Jun. 21, 2021. https://blog.csdn.net/qq\_41895633/article/details/118082431 (accessed Aug. 25, 2025).
- [3] “衰落信道(Fading Channel)的分类与个人理解,” 知乎, Mar. 15, 2024. https://zhuanlan.zhihu.com/p/687300044 (accessed Aug. 25, 2025).
- [4] Recommendation (1997) ITU-R M.1225. *Guidelines for Evaluation of Radio Transmission Technologies for
  IMT-2000*.
- [5] J. Hoydis et al. *Sionna*. (2022) NVIDIA CORPORATION. Accessed: Aug. 28, 2025. [Online]. Available: [https://nvlabs.github.io/sionna/](https://nvlabs.github.io/sionna/)

‍
