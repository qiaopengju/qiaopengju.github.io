---
title: OpenGL
hide: false
math: true
mermaid: false
comment: true
date: 2021-05-27 13:37:37
categories:
tags:
index_img:
banner_img:
excerpt: 随便做的笔记
---

# OpenGL

<img src="https://learnopengl.com/img/getting-started/opengl.jpg" width = 200>

### OpenGL简介

* 实质: 图形规范，并非API；具体由显卡商实现;
* 特点: 状态机

### GLAT

OpenGL具体代码由硬件厂家实现，调用函数时需要实际函数的指针，GLAT库可以用来将函数指向具体实现。



图形渲染管线

着色器

标准化设备坐标

顶点着色器



### Term

perspective:透视

orthographic:正射投影

frustum:平截头体

---

### 深度测试 Depth Testing

默认禁用，启用深度测试：

```glsl
glEnalbe(GL_DEPTH_TEST);					// 启用深度测试
...
while(renderCondition){
    glClear(GL_DEPTH_BUFFER_BIT);	// 清除深度缓冲
}
```

* glsl中 gl_FragCoord.z表示片段的深度缓冲值

禁止深度缓冲写入

```glsl
glDepthMask(GL_FALSE);						// 深度掩码设为false，禁止写入
```

深度缓冲将深度值映射到[0, 1]，是非线性函数，物体离平截头体近平面越近，精度越高，离近平面越远，精度越低：$F_{depth} = \frac{1/z - 1/near}{1/far - 1/near}$

![](https://learnopengl.com/img/advanced/depth_non_linear_graph.png)

深度测试函数，规定在什么情况通过深度测试

```glsl
glDepthFunc(GL_LESS);	// default
/* parameter:
GL_ALWAYS
GLNEVER
GL_LESS
GL_EQUAL
GL_LEQUAL
GL_GRETER
GL_GEAUAL
GL_NOTEQUAL
*/
```

#### 深度冲突 Z-fighting

当物体紧密排列时，深度缓冲没有足够精度决定谁在前面，两个形状抢夺显示先后顺序。会有奇怪的显示，冲突解决方法：

* 不要把物体摆放太近
* 使用高精度深度缓冲
* 近平面设置远一点

---

### 模板测试 Stencil testing

由窗口库(GLFW)配置模板缓冲，默认禁用，启用模板测试：

```glsl
glEnable(GL_STENCIL_TEST);		// 启用模板缓冲
...
while(renderCondition){
    glClear(GL_STENCIL_BUFFER_BIT);	// 清除模板缓冲
}
```

模板掩码，stencil写入缓冲前先与stencil mask做与运算

```glsl
glStencilMask(0xFF);	// 每位写入模板缓冲都会保持原样
glStencilMask(0x00);	// 所有写入缓冲都为0，相当于禁用写入
```

* 模板函数：

````glsl

glStencilFunc(GLenum func, GLint ref, GLuint mask);		// 何时通过模板测试
glStencilFunc(GL_EQUAL, 1, 0xFF);	// default
/* parameter:
GL_ALWAYS
GLNEVER
GL_LESS
GL_EQUAL
GL_LEQUAL
GL_GRETER
GL_GEAUAL
GL_NOTEQUAL
*/
glStencilOp(GLenum sfail, GLenum dpfail, GLenum dppass);
// sfail：模板测试失败时采取的行为
// dpfail: 模板测试通过，深度测试失败
// dppass: 深度测试通过时采取的行为
glStencilOp(GL_KEEP, GL_KEEP, GL_KEEP);	// default
/* parameter:
GL_KEEP
GL_ZERO
GL_REPLACE
GL_INCR: 模板值加一(加一后要小于模板最大值)
GL_INCR_WRAP:	模板值加一，若超过最大值置零
GL_DECR
GL_DECR_WRAP:	模板值减一，若小于0则置为最大值
GL_INVERT:	按位翻转
*/
````

用模板测试绘制物体边框

---

### Blending

* source：源颜色，上面叠的颜色
* destination：目标颜色，底色

destination和source和绘制顺序相关

---

### Framebuffer



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