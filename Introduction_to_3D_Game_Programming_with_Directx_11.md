#1.向量代数
##1.1 向量
###1.1.1 向量与坐标系
* 介绍向量的概念，向量由指向和长度组成，与位置无关
* 引入坐标系统，向量可以用坐标来表示
* 引入frame概念，在不同的frame，同样的向量有不同的坐标。3D计算机图形学就是处理不同frame中的向量坐标转换

###1.1.2 左手坐标系与右手坐标系
###1.1.3 基本向量计算
* zero-vector 是特殊的向量

##1.2 向量长度与单位向量
* 向量的长度通过坐标计算,表示为 $||u||$
* 向量的方向可以通过将向量标准化(normalize)长度来表示

##1.3 向量点乘
**点乘**也称为**点积**，是将两个向量作为输入的函数，返回结果是一个标量。它的结果是**欧几里得空间**的标准**内积**，两个向量的点积写作 $a \cdot b$，**数量积**及**标量积(Scalar Product)**。**点积**是**内积**(Inner Product)的一种特殊形式。
* 向量点乘运算(与向量间的夹角有关)，几何学上的应用是在两个互相垂直的方向上对向量进行分解
* 向量的点乘满足分配率,$a \cdot b=||a||\,||b||\cos\theta$
* 点乘的几何意义是计算两个向量之间的夹角(此时两个向量都是单位向量)，以及在某一方向上的标量投影，这在3D中很有用处。
$$ \cos\theta = \dfrac{a \cdot b}{|a||b|} $$
向量 a 在向量 b 上的标量投影是指:
$$ a_{b} = |a|\cos\theta $$
(*比如：3D技术中的光栅化(光栅化的任务是为了绘制每个三角形单元，如何计算构成三角形单元的每个像素的颜色值)过程中，
我们可以根据两个面的法向量的点乘判断两个面是否处于同一面，如果不是，那么只要光栅化其中需要显示出来的一面，
而另一面我们就不用光栅化它（因为我们根本看不到被遮住的面），这样就节省了很多很多计算，能加快效率。*)
* 如果一组向量是两两正交的，那么我们称之为标准正交集(orthonormal)
* 标准正交集在运算中会丢失精度(因为涉及浮点计算),介绍了如何校正 **正交集合**
$u=(u_x, \;u_y, \;u_z)$
$v=(v_x, \;v_y, \;v_z)$
$u \cdot v=u_xv_x+u_yv_y+u_zv_z$

存在向量 v 和单位向量 n, 应用点乘方法求 v 在与 n 方向上的分量 p 
因为 $||n||=1$，存在标量 k 使得 
$p=kn=(||v||\cos\theta)n=(||v|| \cdot 1 \cos\theta)n=(||v||\,||n|| \cos\theta)n=(v \cdot n)n$
得出标量公式 $k=v \cdot n$, 如果 n 不是单位向量, 那么我们可以得到上述公式更一般的形式:
$p=proj_n(v)=(v \cdot \dfrac{n}{||n||})\dfrac{n}{||n||}=\dfrac{(v \cdot n)}{||n||^2}n$
最后依据公式 $\quad v=p+w=proj_n(v)+perp_n(v)$
v 在 n 方向上的分量为 $p=\dfrac{(v \cdot n)}{||n||^2}n$ 
v 在与 n 垂直的方向上的分量为 $w = v - p$


##1.4 向量叉乘
&emsp;&ensp;&ensp;$u=(u_x, \;u_y, \;u_z)$
&emsp;&ensp;&ensp;$v=(v_x, \;v_y, \;v_z)$
&emsp;&ensp;&ensp;$u \times v=(u_yv_z-u_zv_y, \;u_zv_x-u_xv_z, \;u_xv_y-u_yv_x)$

* 向量的叉乘运算，几何学上的意义是获取一个向量, 该向量垂直于两个乘数组成的平面
* 向量的叉乘不满足乘法交换律   $u \times v \neq v \times u$
* 在3维几何中，叉乘的结果也是一个向量，这个向量就是大名鼎鼎的"法向量"
* 介绍三向量正交集如何用乘法校正

##1.5 点
* 我们用起点在原点的向量代表三维空间中的一点，称为位置向量(position vector)

##1.6 XNA 向量运算
* SSE2 (Streaming SIMD Extensions 2) 指令集


#2.矩阵代数
3D计算机图形学中，我们使用各种矩阵来描述几何转换和在不同的frame中转换坐标
矩阵可以被视为向量的集合

##2.1 定义
* 介绍矩阵的相等,相加,标量乘法和减法
* 相加的矩阵必须是行和列相同的矩阵

$\begin{pmatrix} A_{11}&A_{12}&A_{13}\\ A_{21}&A_{22}&A_{23} \\ A_{31}&A_{32}&A_{33} \end{pmatrix} =\begin{pmatrix} \leftarrow A_{1,*}\rightarrow \\ \leftarrow A_{2,*}\rightarrow \\ \leftarrow A_{3,*}\rightarrow \end{pmatrix}$

$A_{1,*} = [A_{11},\; A_{12},\; A_{13}]$
$A_{2,*} = [A_{21},\; A_{22},\; A_{23}]$
$A_{3,*} = [A_{31},\; A_{32},\; A_{33}]$


$\begin{pmatrix} A_{11}&A_{12}&A_{13}\\ A_{21}&A_{22}&A_{23} \\ A_{31}&A_{32}&A_{33} \end{pmatrix} =\begin{pmatrix} \uparrow&\uparrow&\uparrow\\ A_{*,1}&A_{*,2}&A_{*,3} \\ \downarrow&\downarrow&\downarrow \end{pmatrix}$

$A_{*,1} = \begin{pmatrix}A_{11} \\ A_{21} \\ A_{31}\end{pmatrix}$
$A_{*,2} = \begin{pmatrix}A_{12} \\ A_{22} \\ A_{32}\end{pmatrix}$
$A_{*,3} = \begin{pmatrix}A_{13} \\ A_{23} \\ A_{33}\end{pmatrix}$


##2.2 矩阵乘法
* 两个矩阵 A,B 相乘的前提条件是A的列数与B的行数相同
* 矩阵乘法不满足交换律 $AB \neq BA$

###2.2.1 定义
A 为 $m \times n$ 矩阵, B 为 $n \times p$ 矩阵, A与B叉乘的结果为 $m \times p$ 矩阵 C
矩阵 C 中的元素 $C_{ij}$ 即为 $A_{i,*}$ 与 $B_{*,j}$ 的点乘
$C_{ij} = A_{i,*} \cdot B_{*,j}$ 

###2.2.2 向量与矩阵的乘法
向量 u 为 $[x,\;y,\;z]$
矩阵A 大小为 3*3
$A_{1,*}$  $A_{2,*}$  $A_{3,*}$ 为矩阵A的行向量
$A_{*,1}$  $A_{*,2}$  $A_{*,3}$ 为矩阵A的列向量

$uA = [x,y,z] \begin{pmatrix} A_{11}&A_{12}&A_{13} \\ A_{21}&A_{22}&A_{23} \\ A_{31}&A_{32}&A_{33} \end{pmatrix} = [x,y,z] \begin{pmatrix} \uparrow&\uparrow&\uparrow\\ A_{*,1}&A_{*,2}&A_{*,3} \\ \downarrow&\downarrow&\downarrow \end{pmatrix}$
$\quad\;\,\,= [u \cdot A_{*,1},\quad u \cdot A_{*,2},\quad u \cdot A_{*,3}]$
$\quad\;\,\,=xA_{1,*} + yA_{2,*} + zA_{3,*}$

结论 $\quad uA = xA_{1,*} + yA_{2,*}+ zA_{3,*}$
向量与矩阵的叉乘可以转化为标量与矩阵行向量的乘法
u为标量系数,$A_{1,*}$ 为矩阵 A 的列向量, uA被称为两者的线性组合

###2.2.2 结合律
矩阵乘法满足结合律
(AB)C = A(BC)

##2.3 矩阵移项(transpose)
将 $m \times n$ 矩阵转换为 $n \times m$ 矩阵

##2.4 单位矩阵(identity matrix)
单位矩阵行与列相等，且从左上角到右下角对角线上的元素都为1
如果M是方形矩阵，那么M与单位矩阵I的乘法满足交换律，换句话说，
单位矩阵在矩阵乘法中类似于自然数乘法中的1:
MI = IM = M
存在单位矩阵I与矩阵A，B，如果 AI 能够相乘 或 IB能够相乘，存在等式:
AI = A 
IB = B

##2.5 矩阵行列式(Determinant)
克莱姆法则(Cramer's Rule)
行列式(Determinant)是数学中的一个函数，定义是将 $n \times n$ 矩阵 A 映射到det域，取值为一个实数标量，记作 det(A) 或 |A|

###2.5.1 余子式(Matrix Minors)
把一个方阵A中的第i行、第j列去掉后，剩余的元素组成的 n-1 阶方阵的行列式值，就是 Minor。
Minor的记号是: $M_{ij}.M_{ij}$，余子式也是一个方阵

###2.5.2 定义
矩阵的行列式是递归定义的,例如,4 × 4 矩阵的行列式定义依赖于 3 × 3 矩阵，3 × 3 矩阵的行列式定义依赖于 2 × 2 矩阵，
2 × 2 矩阵的行列式定义依赖于 1 × 1 矩阵, 1 × 1 矩阵也就是矩阵中的一个元素
对于任意阶行列式，都可以改写为第一行所有元素与对应 n-1 阶余子式的积，以此不断递推，直到分为某项与二阶行列式的积，然后再自此回溯最终可得解

##2.6 伴随矩阵定义(Adjoint Matrix)
伴随矩阵就是先计算出余子式的行列式，排列成矩阵,再对矩阵进行转置  
这里注意余子式的行列式开头乘（-1）的次数，即正负性
矩阵 A 的伴随矩阵记为 $A^*$


##2.7 逆矩阵定义(Inverse Matrix)
矩阵代数并没有定义除法，但是定义了乘法逆运算，下面列出了关于逆运算的一些重要信息:
1.只有方阵才是可逆的，本书中只要提到矩阵逆运算，我们假定我们处理的是方阵
2.n × n 矩阵 M 的逆矩阵记为 $M^{-1}$。
3.不是每个方阵都是可逆的，存在逆矩阵的矩阵被称为是可逆的，不存在逆矩阵的矩阵被称为奇异矩阵。
4.逆矩阵如果存在,那么它是唯一的。
5.矩阵与对应的逆矩阵相乘的结果是单位矩阵 $MM^{-1} = M^{-1}M = I$。注意矩阵与逆矩阵的乘法支持乘法交换律。

矩阵逆运算可用于矩阵等式求解。例如，我们有一个等式 p' = pM,p' 和 M 是已知的，求解 p。假设矩阵 M 是可逆的, 我们可以用下面的解法:
等式两边都乘以 M 的逆矩阵
$p'M^{-1} = pMM^{-1}$
因为 $MM^{-1} = I$，得到
$p'M^{-1} = pI$
因为 $pI = p$，得到
$p'M^{-1} = p$

计算逆矩阵的公式 $A^{-1} = \frac {A^*} {detA}$
对于 $n \times n$ 可逆矩阵 A, B, 存在公式 $(AB)^{-1} = B^{-1} A^{-1}$


##2.8 XNA 矩阵



#3.转换
我们用几何学在计算机世界中表达真实世界中的物体，换句话说，是用一堆三角形来近似的描绘物体的表面。如果所有的物体都是静止的，那么这个世界将是无趣的。所以我们对几何转换方法非常有兴趣，所谓的几何转换包括转化,旋转和缩放。在本章中，我们开发了一些用于转换3D空间中点和向量的矩阵等式。

##3.1 线性转换

###3.1.1 定义
对于向量 u, v，标量 k
$u = (u_x, \,u_y, \,u_z)$
$v = (v_x, \,v_y, \,v_z)$
定义函数 $\tau (v)  = \tau(x, y, z) = (x', y', c')$, 该函数的输入和输出都是三维向量

如果下面两个等式成立，那么我们把 τ 称为线性转换
$\tau(u + v) = \tau(u) + \tau(v)$
$\tau(ku) = k\tau(u)$ 

公式推导, a,b,v 为标量, u,v,w 为向量:
$\tau(au + bv + cw) = \tau(au + (bv + cw)) $
$\quad\quad\quad\quad\quad\quad\quad\,\,= a\tau(u) + \tau(bv + cw) $ 
$\quad\quad\quad\quad\quad\quad\quad\,\, = a\tau(u) + b\tau(v) + c\tau(w) $

线性变换从几何上直观的来看有三个要点：
1.变换前是直线的，变换后依然是直线
2.直线比例保持不变
3.变换前是原点的，变换后依然是原点


###3.1.2 向量线性变换的矩阵表达
待变换向量 u 的定义如下:
$u = (x, \,\,y, \,\,z)$
$u = (x, \,\,y, \,\,z) = xi + yi + zk = x(1,0,0)+y(0,1,0)+z(0,0,1)$
(上面公式的推导参考 2.2.2 向量与矩阵的乘法)

该公式的意义是证明了三维空间中的任意向量都可以由三个向量的线性组合来表示，这里的 i, j, k 被称为 $\mathbb{R}^3$ (指所有三维向量的集合) 的 `单位基向量`(standard basis vector)

标准基向量表示一组长度为1的基
标准正交基表示一组长度为1且两两正交的基<br><br>


下面引入线性变换函数 $\tau(u)$ 的定义:

$\tau(u) = x\tau(i) + y\tau(j) + z\tau(k) $

如果<br>
$\tau(i) = \left(A_{11},\,A_{12},\,A_{13}\right)$
$\tau(j) = (A_{21},\,A_{22},\,A_{23})$
$\tau(k) = (A_{31},\,A_{32},\,A_{33})$

那么可以得到<br>

$\tau(u) =uA=[x,y,z] \begin{pmatrix} \leftarrow \tau(i)\rightarrow \\ \leftarrow \tau(j)\rightarrow \\ \leftarrow \tau(k)\rightarrow \end{pmatrix}=[x,y,z] \begin{pmatrix} A_{11}&A_{12}&A_{13} \\ A_{21}&A_{22}&A_{23} \\ A_{31}&A_{32}&A_{33} \end{pmatrix}$ 
<br>

此时由行向量 $\tau(i),\,\,  \tau(j),\,\,  \tau(k)$ 组成的矩阵 A 称为线性变换的矩阵表达, 矩阵 A 在这里可以看做线性变换的系数,选定基底实际是选定坐标轴(不一定正交),三维空间中的任意向量都是基向量的线性组合。

###3.1.3 缩放
缩放操作的数学定义
$S(x, y, z) = (s_{x}x, \;s_{y}y, \;s_{z}z)$
$s_x, \; s_y, \; s_z$ 分别是 x, y, z 轴上的缩放单位，都是标量

缩放交换律推导:

$S(u+v)=\begin{pmatrix} S_x(u_x+v_x),&S_y(u_y+v_y),&S_z(u_z+v_z) \end{pmatrix}$
$\quad\quad\quad\quad=\begin{pmatrix} S_xu_x+S_xv_x,&S_yu_y+S_yv_y),&S_zu_z+S_zv_z) \end{pmatrix}$
$\quad\quad\quad\quad=\begin{pmatrix} S_xu_x,&S_yu_y,&S_zu_z \end{pmatrix} + \begin{pmatrix} S_xv_x,&S_yv_y,&S_zv_z \end{pmatrix}$
$\quad\quad\quad\quad=S(u) + S(v)$

对于标量 k,则有<br>
$S(ku)=\begin{pmatrix} S_xku_x,&S_yku_y,&S_zku_z \end{pmatrix}$
$\quad\quad\;\;\;=k\begin{pmatrix} S_xku_x,&S_yku_y,&S_zku_z \end{pmatrix}$
$\quad\quad\;\;\;=kS(u)$
<br>
由上可知, S 也是线性的，并且存在矩阵表达:
<br>
$S(i)=\begin{pmatrix} S_x \cdot 1,&S_y \cdot 0,&S_z \cdot 0 \end{pmatrix}=\begin{pmatrix} S_x,0,0 \end{pmatrix}$
$S(j)=\begin{pmatrix} S_x \cdot 0,&S_y \cdot 1,&S_z \cdot 0 \end{pmatrix}=\begin{pmatrix} 0,S_y,0 \end{pmatrix}$
$S(k)=\begin{pmatrix} S_x \cdot 0,&S_y \cdot 0,&S_z \cdot 1 \end{pmatrix}=\begin{pmatrix} 0,0,S_z \end{pmatrix}$
<br>
缩放操作的矩阵表达如下，又名缩放矩阵:
<br>
$\begin{pmatrix} S_x&0&0 \\ 0&S_y&0 \\ 0&0&S_z \end{pmatrix}$
<br>
缩放矩阵对应的逆矩阵为:
<br>
$\begin{pmatrix} 1/Sx&0&0 \\ 0&1/S_y&0 \\ 0&0&1/S_z \end{pmatrix}$
<br>
<br>

###3.1.4 旋转
* 旋转公式 $R_n(v)$ 的推导

&emsp;&ensp;&ensp;场景: 向量 v 绕 n 轴旋转了 θ 角度, 假设 $||n||=1$
&emsp;&ensp;&ensp;根据上述场景推导向量旋转矩阵，并证明旋转也属于线性转换

&emsp;&ensp;&ensp;将向量f分解为两个子向量, 其中一个与 n 轴平行, 记为 $proj_n(v)$, 
&emsp;&ensp;&ensp;另外一个子向量与 n 轴垂直, 记为 $v\bot=perp_n(v)=v-proj_n(v)$
&emsp;&ensp;&ensp;因为 n 是一个单位向量, 存在 $proj_n(v)=(n \cdot v)v\quad\quad$ (参考 1.3)
&emsp;&ensp;&ensp;在旋转过程中, 子向量 $proj_n(v)$ 是不变的, 所以我们得到旋转公式:
&emsp;&ensp;&ensp;$R_n(v)=proj_n(v)+R_n(v \bot)$
<br>
* $R_n(v \bot)$ 的推导

&emsp;&ensp;&ensp;$R_n(v \bot)=\cos\theta \, v\bot + \sin\theta(n \times v)$
<br>
* 总结

$R_n(v)=proj_n(v)+R_n(v \bot)$
$\quad\quad\;\;\;=(n \cdot v)n+\cos\theta v\bot+\sin\theta(n\times v)$
$\quad\quad\;\;\;=(n \cdot v)n+\cos\theta (v-(n\cdot v)n)+\sin\theta(n\times v)$
$\quad\quad\;\;\;=\cos\theta v+(1- \cos\theta) (n\cdot v)n+\sin\theta(n\times v)$

使 $c  = \cos \theta$ , $s  = \sin \theta$ , 根据线性变换公式可得:
$R_n=\begin{pmatrix} c+(1-c)x^2&(1-c)xy+sz&(1-c)xz-sy \\(1-c)xy-sz&c+(1-c)y^2&(1-c)yz+sx \\ (1-c)xz+sy&(1-c)yz-sx&c+(1-c)z^2 \end{pmatrix}$
<br>
$R_x=\begin{pmatrix} 1&0&0&0 \\0&\cos\theta&\sin\theta&0\\ 0&-\sin\theta&\cos\theta&0\\ 0&0&0&1 \end{pmatrix}$
<br>
$R_y=\begin{pmatrix} \cos\theta&0&-\sin\theta&0 \\0&1&0&0 \\\sin\theta&0&\cos\theta&0\\ 0&0&0&1 \end{pmatrix}$
<br>
$R_z=\begin{pmatrix} \cos\theta&\sin\theta&0&0 \\-\sin\theta&\cos\theta&0&0 \\0&0&1&0\\ 0&0&0&1 \end{pmatrix}$

##3.2 仿射转换(Affine Transformations)
指在几何中，一个向量空间进行一次线性变换并接上一个平移，变换为另一个向量空间。
仿射变换从几何直观只有两个要点：
1.变换前是直线的，变换后依然是直线
2.直线比例保持不变


###3.2.1 齐次坐标(homogeneous coordinates)
在数学里，齐次坐标(homogeneous coordinates)就是将一个原本是n维的向量用一个n+1维向量来表示，是指一个用于投影几何里的坐标系统,又被称为投影坐标(projective coordinates)。



$\begin{pmatrix}- B \left(k_{x}^{2} + k_{y}^{2}\right) + C - D \left(k_{x}^{2} + k_{y}^{2}\right) + M & A \left(k_{x} + i k_{y}\right) & 0 & 0\\A \left(k_{x} - i k_{y}\right) & B \left(k_{x}^{2} + k_{y}^{2}\right) + C - D \left(k_{x}^{2} + k_{y}^{2}\right) - M & 0 & 0\\0 & 0 & - B \left(k_{x}^{2} + k_{y}^{2}\right) + C - D \left(k_{x}^{2} + k_{y}^{2}\right) + M & A \left(- k_{x} + i k_{y}\right)\\0 & 0 & - A \left(k_{x} + i k_{y}\right) & B \left(k_{x}^{2} + k_{y}^{2}\right) + C - D \left(k_{x}^{2} + k_{y}^{2}\right) - M\end{pmatrix}$


gaojun 00301069






