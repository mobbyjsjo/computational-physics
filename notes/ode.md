# 常微分方程 (ODE) 数值求解 - 知识点总结

---

## 一、常微分方程的基本定义

### 1.1 导数的极限定义

$$f'(x)=\lim_{h\to 0}\frac{f(x+h)-f(x)}{h}$$

- 割线 (secant line): 连接曲线上两点的直线，斜率为差商 $\frac{f(x+h)-f(x)}{h}$
- 切线 (tangent line): $h\to 0$ 时割线的极限，斜率为导数 $f'(x)$

### 1.2 常微分方程 (ODE) 的一般形式

n 阶常微分方程:

$$F(t, y, y', y'', \ldots, y^{(n)}) = 0$$

- $y = y(t)$ 是未知函数，$t$ 是自变量
- $y^{(k)} = \frac{d^k y}{dt^k}$ 表示 $y$ 的 $k$ 阶导数
- **阶数**: 方程中最高阶导数的阶数 $n$

### 1.3 解的本质

- ODE 的解是一个**函数** $y(t)$，不是单个数值
- 解依赖于自变量 $t$（通常代表时间）
- $y$ 可以是标量函数（如 $r(t)$）或矢量函数（如 $r(t)$ 的三维位置向量）

---

## 二、物理学中的常微分方程实例

### 2.1 牛顿第二定律

$$F = ma,\quad m\frac{dv}{dt} = F(t)$$

### 2.2 弹簧振动方程（阻尼谐振子）

$$m r'' + C r' + k r = f(t)$$

参数说明:

- $m$: 质量
- $r(t)$: 位移, $r' = v$ (速度), $r'' = a$ (加速度)
- $C$: 阻尼系数
- $k$: 弹簧常数 (spring constant)
- $f(t)$: 外力

### 2.3 N 体问题

$$\mathbf{r}_i'' = -\sum_{j=1,j\ne i}^{N} \frac{G m_j}{|\mathbf{r}_j - \mathbf{r}_i|^3} (\mathbf{r}_j - \mathbf{r}_i)$$

- $N=2$: 二体问题（可解析求解，开普勒定律）
- $N=3$: 三体问题（一般不可积）
- $N>3$: 多体问题（需要数值方法）
- 应用: 天体力学、星系形成、宇宙大尺度结构

### 2.4 定态薛定谔方程 (1D)

$$-\frac{\hbar^2}{2m} \frac{d^2\psi}{dx^2} + V(x)\psi = E\psi$$

- $\hbar$: 约化普朗克常数
- $\psi(x)$: 波函数
- $V(x)$: 势能函数
- $E$: 能量本征值

### 2.5 欧拉方程（刚体旋转动力学）

$$\mathbf{I}\,\dot{\boldsymbol{\omega}} + \boldsymbol{\omega} \times (\mathbf{I}\boldsymbol{\omega}) = \boldsymbol{\tau}$$

- $\mathbf{I}$: 惯量张量
- $\boldsymbol{\omega}$: 角速度矢量
- $\boldsymbol{\tau}$: 力矩

### 2.6 一维热平衡态

$$\frac{d^2T}{dx^2} = 0$$

### 2.7 洛伦兹方程（混沌系统）

$$\frac{dx}{dt} = \sigma (y - x)$$
$$\frac{dy}{dt} = x(\rho - z) - y$$
$$\frac{dz}{dt} = xy - \beta z$$

- $\sigma$: 普朗特数
- $\rho$: 瑞利数
- $\beta$: 几何因子

### 2.8 含空气阻力的运动方程

$$m\dot{v} = f(t) - k v^2$$

- $k$: 阻力系数
- 含 $v^2$ 项表示二次阻力

---

## 三、降阶法：高阶 ODE 转化为一阶 ODE 组

### 3.1 核心原理

**大于一阶的常微分方程都可以通过定义中间变量，转为一阶常微分方程组。**

### 3.2 二阶 ODE 的转化（以弹簧振动为例）

原方程:

$$m r'' + C r' + k r = f(t)$$

定义 $v = r'$ (速度)，转化为:

$$\begin{aligned}
 r' &= v \\
 v' &= \frac{f(t) - C v - k r}{m}
\end{aligned}$$

利用一阶求解方法同时求解 $r$ 和 $v$。

### 3.3 n 阶 ODE 的一般降阶方法

定义 $n-1$ 个新变量:

$$a = y'$$
$$b = y'' = a'$$
$$c = y''' = b'$$
$$\ldots$$
$$z = y^{(n-1)}$$

转化为 $n$ 个一阶 ODE:

$$\begin{aligned}
 y' &= a \\
 a' &= b \\
 b' &= c \\
 &\,\vdots \\
 z' &= F(t, y, a, b, c, \ldots)
\end{aligned}$$

### 3.4 推广到 n 阶 m 维 ODE 组

n 阶 m 维 ODE ���可以转化为 $n\cdot m$ 维的一阶 ODE 组。

---

## 四、数值求解 ODE 的基本原理与步骤

### 4.1 基本原理：逐步积分

在离散时间点 $t_0, t_1, \ldots, t_n$ 处逐步求解近似值 $y(t_n)$。

### 4.2 步骤

**Step 1: 改写方程形式**

$$y'(t) = F(y, t)$$

**Step 2: 由初始条件积分得到 $y(t_1)$**

$$y(t_1) = y(t_0) + \int_{t_0}^{t_1} F(y, t)\,dt$$

**Step 3: 迭代求解后续时间步**

$$y(t_2) = y(t_1) + \int_{t_1}^{t_2} F(y, t)\,dt$$
$$\cdots$$
$$y(t_n) = y(t_{n-1}) + \int_{t_{n-1}}^{t_n} F(y, t)\,dt$$

### 4.3 数值求解的特点

- **局限**: 无法得到 $y(t)$ 的解析表达式，只在离散点 $t_n$ 处给出近似解
- **优势**: 可以预测无法解析求解的复杂系统的演化（如三体运动轨迹）
- 数值积分存在近似误差，且随时间步数积累

### 4.4 数值求解的一般流程

1. 建立微分方程模型
2. 设定初始条件与参数
3. 使用数值方法求解
4. 分析误差

---

## 五、scipy.integrate.odeint 求解 ODE

### 5.1 一阶一维 ODE 示例

方程:

$$m\dot{v} = f(t) - k v^2$$

代码步骤:

1. 定义导数函数 `func(v, t, m, k)`
2. 设置初始条件 $v_0$ 和时间列表 $tlist = [t_0, t_1, \ldots, t_n]$
3. 调用 `odeint`:
   $$vlist = odeint(func, v_0, tlist, args=(m, k))$$

### 5.2 二阶 ODE 的 odeint 求解（弹簧振动）

方程:

$$m r'' + C r' + k r = f(t)$$

降阶后的导数函数:

- 输入: $y = [r, v]$ (状态向量)
- 输出: $y' = [r_{\dot{}}, v_{\dot{}}]$
  - $r_{\dot{}} = v$
  - $v_{\dot{}} = \frac{f(t) - C v - k r}{m}$

代码:

- $y_0 = [r_0, v_0]$ (初始位移和初始速度)
- $$ylist = odeint(func, y_0, tlist, args=(m, C, k))$$

---

## 六、数值方法（Euler 法与 Runge-Kutta 法）

### 6.1 显式欧拉法 (Forward/Explicit Euler)

$$y_{n+1} = y_n + h f(x_n, y_n)$$

- 局部截断误差: $O(h^2)$
- 全局误差: $O(h)$
- 一阶方法

### 6.2 隐式欧拉法 (Backward/Implicit Euler)

$$y_{n+1} = y_n + h f(x_{n+1}, y_{n+1})$$

- 需要求解关于 $y_{n+1}$ 的方程（通常用迭代法）
- 具有更好的数值稳定性

### 6.3 四阶 Runge-Kutta 方法 (RK4)

$$\begin{aligned}
 k_1 &= h f(x_n, y_n) \\
 k_2 &= h f\left(x_n + \frac{h}{2}, y_n + \frac{k_1}{2}\right) \\
 k_3 &= h f\left(x_n + \frac{h}{2}, y_n + \frac{k_2}{2}\right) \\
 k_4 &= h f(x_n + h, y_n + k_3)
\end{aligned}$$

$$y_{n+1} = y_n + \frac{k_1 + 2k_2 + 2k_3 + k_4}{6}$$

- 局部截断误差: $O(h^5)$
- 全局误差: $O(h^4)$
- 四阶方法
- 最常用的 ODE 数值求解方法之一

---

## 七、误差分析

### 7.1 误差类型

- **局部截断误差 (Local Truncation Error)**: 单步数值方法引入的误差
  - $p$ 阶方法的局部截断误差为 $O(h^{p+1})$

- **全局误差 (Global Error)**: 所有步骤累积的误差
  - $p$ 阶方法的全局误差为 $O(h^p)$

- **舍入误差 (Round-off Error)**: 计算机浮点运算引入的误差

### 7.2 稳定性 (Stability)

- 判断误差在迭代过程中是增长还是衰减
- 与步长 $h$ 和方程特性有关

---

## 八、相关物理公式补充

### 8.1 万有引力定律

$$F = G\frac{m_1 m_2}{d^2}$$

### 8.2 波动方程 (1D PDE)

$$\frac{d^2u}{dt^2} = c^2 \frac{d^2u}{dx^2}$$

### 8.3 高斯分布

$$\phi(x) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$$

---

## 九、课程工具与资源

- 编程语言: Python
- 核心库: numpy (数组处理), matplotlib (绘图), scipy (科学计算)
- 求解 ODE: scipy.integrate.odeint
- 参考教材: Numerical Analysis (Timothy Sauer), Numerical Recipes
