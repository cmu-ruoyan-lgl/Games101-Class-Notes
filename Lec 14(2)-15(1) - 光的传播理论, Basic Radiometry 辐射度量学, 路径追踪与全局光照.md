# Lec 14(2)-15(1) - 光的传播理论, Basic Radiometry 辐射度量学, 路径追踪与全局光照

(国内的图形学课程都没有的部分)

Whitted Style Ray Tracing 无法保证正确性

辐射度量学：精准度量光的一系列物理量 → Physically Based

- the basics of Path Tracing
- Measurement system and units for illumination
- Accurately measure the spatial properties of light
  - New terms: Radiant flux, intensity, irradiance, radiance
- Perform lighting calculations in a physically correct manner

## Radiant Energy and Flux (Power)

![image-20221222193930822](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222193930822.png)

https://twitter.com/lo0gic/status/1466248302333075466?s=21

基础物理量

- Radiant energy: 能量，the energy of electromagnetic radiation

  - 符号：Q
  - 单位：J（Joule焦耳）

- Radiant flux (power): the energy emitted, reflected, transmitted or received, 

  per unit time

  . （单位时间能量 → 功率）

  - 符号：ϕ （phi）
  - 单位：W（Watt），lm（lumen流明）
  - 单位时间 很重要

- Flux: number of photons flowing through a sensor in unit time

Important Light Measurements of Interest

- Radiant(luminous) **Intensity**: the power per unit solid angle (立体角) emitted by a point light source.

  - 符号定义：

  ![image-20221222194015123](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194015123.png)

  - 单位：W/sr，lm/sr=cd(candela坎德拉)

  - 立体角

    - 弧度：弧长/半径
    - 弧度在三维的延伸→立体角：面积/半径^2，

    ![image-20221222194027410](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194027410.png)

    - 单位：sr, steradians
    - 球面=4pi sr

    ![image-20221222194038396](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194038396.png)

  - 方向性

    - 用 $\omega$ 来表示方向

  - 点光源：

  ![image-20221222194050138](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194050138.png)

- Irradiance: power per unit area (perpendicular/ projected)

  - Light Falling On A Surface

  ![image-20221222194101675](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194101675.png)

  - 单位：W/m^2 | lm/m^2 = lux
  - 需要注意，是垂直方向
    - Lambert’s Cosine Law
    - 地球的四季
    - Falloff: 点光源的能量散布
  - 无方向性

- **Radiance: power per unit solid angle, per projected unit area.**

  - describes the distribution of light in an environment
  - Light Traveling Along A Ray

  ![image-20221222194118050](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194118050.png)

  - 某个单位面积往某个单位立体角辐射的能量密度

  - Radiance: **Irradiance** per solid angle

  - Radiance: **Intensity** per projected unit area

  - 有方向性

  - Incident radiance is the irradiance per unit solid angle arriving at the surface. 某个小面接受来自某个方向的光线

    ![image-20221222194129371](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194129371.png)

  - Exiting surface radiance is the intensity per unit projected area leaving the surface. 某个小面发出向某个方向的光线

    ![image-20221222194142149](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194142149.png)

Irradiance和Radiance之间的区别就在于是否有方向性

- Irradiance: total power received by area dA 四面八方的光线积分起来
- Radiance: power received by area dA from “direction” dω

![image-20221222194153158](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194153158.png)

- H^2：半球面

## Bidirectional Reflectance Distribution Function (BRDF) 双向反射分布函数

**BRDF描述了从某个方向入射到一个点上的光线的能量会怎么反射，在不同的反射方向上会各分布多少能量**

反射的理解：光线打到某个点，（被吸收了）然后反弹（发出）到其他地方

Radiance from direction ωi turns into the power E that dA（某个点） receives, Then power E will become the radiance to any other direction ωr

![image-20221222194208375](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194208375.png)

能量一般指功率，即单位时间内的能量。

某个点接受/发射光线总能量：Irradiance

某个点从某个方向接受/向某个方向发射光线能量：radiance

- Differential irradiance incoming:

![image-20221222194220307](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194220307.png)

- Differential radiance exiting:

![image-20221222194229307](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194229307.png)

BRDF的几个细节：

- 针对一个输入源
- 描述不同方向的输出
- 定义了不同材质

BRDF：represents how much light is reflected into each outgoing direction dL r (! r ) from each incoming direction

![image-20221222194242736](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194242736.png)

## The Reflection Equation 反射方程

- 针对一个输出源（着色点），积分所有方向输入源（光照）的BRDF，获得最后的输出

![image-20221222194307567](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194307567.png)

Challenge: Recursive Equation

- 不只有光源才是输入源，其他物体的反射光也有可能是输入源
- 递归的计算方式，计算量爆炸！

## The Rendering Equation 渲染方程（绘制方程）

**Rendering Equation (Kajiya 86) 跨时代**

Adding an Emission term to the reflection equation to make it general!

![image-20221222194318197](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194318197.png)

- 包括了物体自己会发光的情况，包括了所有光线的传播情况
- assume that all directions are pointing outwards!
- $H^2$和$\Omega^{+}$ 都代表半球
- 怎么解这个方程呢？下节课

单个点光源：

![image-20221222194338265](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194338265.png)

多个点光源：加起来

![image-20221222194401953](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194401953.png)

还有面光源：积分起来

![image-20221222194419201](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194419201.png)

考虑其他物体反射的光线（把光源也包括在内了）：→递归

![image-20221222194502309](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194502309.png)

![image-20221222194522511](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194522511.png)

这个方程是 Fredholm Integral Equation of second kind [extensively studied numerically] with canonical form → 简写为如下形式

![image-20221222194531766](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194531766.png)

通过算符的抽象还可极度简化成如下形式：L = E + KL （K：反射算符）其中L为未知数

- Can be discretized to a simple matrix equation [or system of simultaneous linear equations] (L, E are vectors, K is the light transport matrix)

![image-20221222194545348](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194545348.png)

![image-20221222194556436](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194556436.png)

![image-20221222194612258](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221222194612258.png)

- 光栅化的着色过程只有直接光照（间接光照需要间接计算）
- 全局光照：直接和间接光照的集合
  - 会收敛到一个亮度

如何解全局光照方程？Monte Carlo Path Tracing (Lec 16)

## Probability Review 概率论回顾

- 随机变量$X$: 可能取很多数值的变量
- 随机变量的分布函数 $X \sim p(x)$: probability density function (PDF) 取不同数值的概率
  - 比如，uniform PDF, 骰子
  - 概率$p_i$的数值非负，和为1，注意和p(x)的概念不一样。是p(x)的输出。
- 期望：The average value that one obtains if repeatedly drawing samples from the random distribution. $E[X]=\sum_{i=1}^{n} x_{i} p_{i}$

连续情况下：

- 随机变量的分布函数 $X \sim p(x)$ 是连续的→**Probability Distribution Function (PDF)**
- 某一个x对应的概率 = p(x)dx （细长条）
- 概率密度函数的性质 $p(x) \geq 0$ and $\int p(x) d x=1$
- 期望：$E[X]=\int x p(x) d x$

随机变量的函数：

- 如果某个随机变量Y是随机变量X的函数：$Y=f(X)$
- 期望的关系：$E[Y]=E[f(X)]=\int f(x) p(x) d x$