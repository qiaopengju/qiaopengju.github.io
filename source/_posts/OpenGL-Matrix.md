---
title: OpenGL-Matrix
hide: false
math: true
mermaid: false
comment: true
date: 2021-06-11 00:45:10
categories: OpenGL
tags:
index_img:
banner_img:
excerpt: OpenGL matrix
---

### Matrix

* Scale:

$$
\begin{bmatrix}
S_1 & 0 & 0 & 0 \\
0 & S_2 & 0 & 0 \\
0 & 0 & S_3 & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{pmatrix}
x_1\\ x2\\ x3\\ 1
\end{pmatrix}
=
\begin{pmatrix}
S_1x_1\\ S_2x_2\\ S_3x_3 \\1
\end{pmatrix}
$$

* Translate:

$$
\begin{bmatrix}
1 & 0 & 0 & T_x\\
0 & 1 & 0 & T_y\\
0 & 0 & 1 & T_z\\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{pmatrix}
x\\ y\\ z\\ 1
\end{pmatrix}
=
\begin{pmatrix}
x+T_x\\ y+T_y\\ z+T_y\\ 1
\end{pmatrix}
$$

<p class="note note-primary">Translate using w(Homogeneous Coordinates-齐次坐标)</p>

* Rotate:

  * 两个向量的内积，在被同一个旋转矩阵M操作后不变，得旋转矩阵是正交矩阵

  $$
  a^T \centerdot b = (Ma)^T \centerdot (Mb) =a^TM^TMa \\ \Rightarrow M^TM = M^{-1}M=E
  $$

  * 二维向量旋转矩阵：

$$
\begin{bmatrix}
cos\theta & -sin\theta\\
sin\theta & cos\theta
\end{bmatrix}
$$

![](rotate.png)

现在推导**顺时针**旋转$\theta$度的旋转矩阵，将$\vec{OP}$顺时针旋转$\theta$度，相当于将坐标系逆时针旋转$\theta$度：
$$
X_b=OB+BC;
\quad OB=X_acos\theta,
\quad BC=AD=APsin\theta = Y_a sin\theta\\
$$

$$
\Rightarrow X_b = X_acos\theta + Y_asin\theta\\
$$

$$
Y_b = OF-GF; 
\quad OF=OE cos\theta=Y_a cos\theta,
\quad GF=EH=EP sin\theta = X_a sin\theta\\
$$

$$
\Rightarrow Y_b = - X_a sin\theta + Y_a cos\theta\\
$$

$$
\Rightarrow
\begin{pmatrix}
X_b\\ Y_b
\end{pmatrix}
=
\begin{bmatrix}
cos\theta & sin\theta\\
-sin\theta & cos\theta
\end{bmatrix}
\begin{pmatrix}
X_a\\ Y_a
\end{pmatrix}
$$

所以顺时针旋转$\theta$度的矩阵是：
$$
\begin{bmatrix}
cos\theta & sin\theta\\
-sin\theta & cos\theta
\end{bmatrix}
$$

<p class="note note-danger">逆时针是正角，上述推导为顺时针旋转矩阵</p>

<p class="note not-info">Transform Order: Scale->Rotate->Translate</p>

---

#### Projection Matrix: 

*本节内容学习自：[OpenGL Projection Matrix][http://www.songho.ca/opengl/gl_projectionmatrix.html]*

OpenGL Projection Matirx Task:

* Clip coordinates from view coordinates to clip coordinates;
* Normalize device coordinates

##### Perspective Projection

1. project 3D point to **near plane**.

![](gl_projectionmatrix03.png)

![](gl_projectionmatrix04.png)

发现view坐标系下$x_e,y_e$坐标投影到近平面后，$x_p$和$y_p$都由${-z_e}$做分母，故我们将投影空间的$w_p$设为$-z_e$，因为OpenGL输出每一个空间坐标的最后，都要除齐次坐标$w$.
$$
\begin{pmatrix}
X_{ndc}\\ Y_{ndc}\\ Z_{ndc}
\end{pmatrix}
=
\begin{pmatrix}
x_{clip}/w_{clip}\\
y_{clip}/w_{clip}\\
z_{clip}/w_{clip}\\
\end{pmatrix}
$$

<p class="note not-info">NDC: normalized device coordinate 标准设备坐标</p>

投影空间的$w_p$为$-z_e$，因此Perspective projection Matrix的第四行确定了：
$$
\begin{pmatrix}
X_c\\ Y_c\\ Z_c\\ W_c
\end{pmatrix}
=
\begin{bmatrix}
\centerdot & \centerdot & \centerdot & \centerdot\\
\centerdot & \centerdot & \centerdot & \centerdot\\
\centerdot & \centerdot & \centerdot & \centerdot\\
0 & 0 & -1 &0
\end{bmatrix}
\begin{pmatrix}
X_e\\ Y_e\\ Z_e\\ W_e(=1)
\end{pmatrix}
$$
之后我们将$X_p,Y_p$从[l, r]映射到[-1, 1]，转换为NDC:
$$
X_{ndc} = \frac{1- (-1)}{r-l} \centerdot X_p + \beta
$$
将裁剪空间边界r,l带入$X_p$，会分别映射为1, -1:
$$
1 = \frac{2r}{r-l} + \beta
$$

$$
\beta = 1-\frac{2r}{r-l}=-\frac{r+l}{r-l}
$$

$$
X_{ndc} = \frac{2X_p}{r-l} - \frac{r+l}{r-l}
$$

同理，求得$Y_p$的映射：
$$
Y_{ndc} = \frac{1- (-1)}{t-b} \centerdot Y_p + \gamma
$$
带入上边界t，映射至NDC的1：
$$
1=\frac{2t}{t-b} + \gamma \Rightarrow \gamma = -\frac{t+b}{t-b}
$$

$$
Y_{ndc} = \frac{2Y_p}{t-b} - \frac{t+b}{t-b}
$$

之后将再做恒等式变换：
$$
X_n =\frac{2X_p}{r-l} - \frac{r+l}{r-l}
=\frac{2\frac{n \centerdot X_e}{-Z_e}}{r-l} - \frac{r+l}{r-l}
$$

$$
X_n = (\underbrace{\frac{2n}{r-l}X_e + \frac{r+l}{r-l}\centerdot Z_e}_{X_c})/-Z_e
$$

$$
Y_n =\frac{2Y_p}{t-b} - \frac{t+b}{t-b}
=\frac{2\frac{n \centerdot Y_e}{-Z_e}}{t-b} - \frac{t+b}{t-b}
$$

$$
Y_n = (\underbrace{\frac{2n}{t-b}Y_e + \frac{r+l}{r-l}\centerdot Z_e}_{Y_c})/-Z_e
$$

分别得到裁剪空间坐标$X_c,Y_c$，现在将Perspective projection Matrix补全：
$$
\begin{pmatrix}
X_c\\ Y_c\\ Z_c\\ W_c
\end{pmatrix}
=
\begin{bmatrix}
\frac{2n}{r-l} & 0 & \frac{r+l}{r-l} & 0\\
0 & \frac{2n}{t-b} & \frac{t+b}{t-b} & 0\\
\centerdot & \centerdot & \centerdot & \centerdot\\
0 & 0 & -1 &0
\end{bmatrix}
\begin{pmatrix}
X_e\\ Y_e\\ Z_e\\ W_e(=1)
\end{pmatrix}
$$
现在我们计算裁剪空间的$Z_c$坐标，需要注意，裁剪空间的$X_c，Y_c$坐标是通过将观察空间的坐标投影到近平面上得到的，近平面的Z坐标恒为-n，所以我们需要不同的方法计算$Z_c$，并且$Z_c$和$Z_e$要是双射。首先$Z_c$肯定和X，Y坐标无关，与$Z_e,W_e$有关，观察空间的$W_e$为1，故$Z_c$只与$Z_e$相关，设：
$$
\begin{pmatrix}
X_c\\ Y_c\\ Z_c\\ W_c
\end{pmatrix}
=
\begin{bmatrix}
\frac{2n}{r-l} & 0 & \frac{r+l}{r-l} & 0\\
0 & \frac{2n}{t-b} & \frac{t+b}{t-b} & 0\\
0 & 0 & A & B\\
0 & 0 & -1 &0
\end{bmatrix}
\begin{pmatrix}
X_e\\ Y_e\\ Z_e\\ W_e(=1)
\end{pmatrix}
$$

$$
Z_{ndc} = Z_c/W_c = \frac{AZ_e+BW_e}{-Z_e} =\frac{AZ_e+B}{-Z_e}
$$

带入近平面远平面$Z_e$被映射为[-1,1]，带入得：
$$
\frac{-An+B}{n}=-1\\
\frac{-Af+B}{n}=1
$$

$$
\Rightarrow A=-\frac{f+n}{f-n};\quad B=-\frac{2fn}{f-n}
$$

$$
Z_{ndc} = \frac{-\frac{f+n}{f-n}Z_e-\frac{2fn}{f-n}}{-Z_e}
$$

注意，$Z_n$和$Z_e$是的关系不是线性的，是成反比的，越靠近近平面Z的精度越高，越远离近平面Z的精度越低，这样会导致深度测试冲突(Z-fighting)。

![](gl_projectionmatrix07.png)

最后终于得到透视投影的投影矩阵：
$$
\begin{bmatrix}
\frac{2n}{r-l} & 0 & \frac{r+l}{r-l} & 0\\
0 & \frac{2n}{t-b} & \frac{t+b}{t-b} & 0\\
0 & 0 & -\frac{f+n}{f-n} & -\frac{2fn}{f-n}\\
0 & 0 & -1 &0
\end{bmatrix}
$$

#### Orthographic Projection:

正射投影不需要对观察空间坐标做过多变换，直接将坐标裁剪并映射到[-1,1]即可。

#### View Matrix

定义了摄像机空间的基坐标后，我们将世界坐标系转换为摄像机的坐标系，也就是观察空间坐标，先做平移变换，再做基变换，其中R,U,D,P分别是摄像机的右轴，上轴，方向向量，位置：
$$
LookAt = 
\begin{bmatrix}
R_x & R_y & R_z & 0\\
U_x & U_y & U_z & 0\\
D_x & D_y & D_z & 0\\
0 & 0 & 0 & 1
\end{bmatrix}
*
\begin{bmatrix}
1 & 0 & 0 & -P_x\\
0 & 1 & 0 & -P_y\\
0 & 0 & 1 & -P_z\\
0 & 0 & 0 & 1
\end{bmatrix}
$$

<p class="note note-danger">注意，摄像机与世界位移方向相反，所以我们将世界坐标平移-Camera.position</p>