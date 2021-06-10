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

<p class="note not-info">Translate Order: Scale->Rotate->Translate</p>

* Perspective(透视投影):   

