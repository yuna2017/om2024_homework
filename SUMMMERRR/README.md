# 运维部寒假个人作业

### 问题

HPL和HPCG（15分）

Proposal 需要包含软件环境（操作系统，编译器，数学库， MPI 软件，软件版本等）的描述，测试方法，性能优化方法，性能评价，问题和解决方案等。深入的分析 HPL 和 HPCG 算法和源代码是一个加分项。

#### 运行设备

系统信息 Linux ubuntu-VMware-Virtual-Platform 6.8.0-51-generic #52-Ubuntu SMP PREEMPT_DYNAMIC Thu Dec  5 13:09:44 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux

系统版本 Ubuntu 24.04.1 LTS

DirectMap1G:     6291456 kB 总内存分配为8GB

逻辑CPU个数：16

物理CPU个数：8

**CPU型号：AMD Ryzen 7 8845H w/ Radeon 780M Graphics**

基准时钟频率 3.8 GHz

最高加速时钟频率 最高可达 5.1 GHz

#### 软件信息

C语言编译器 ：gcc (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0

C++编译器 ：g++ (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0

Fortran编译器 ：GNU Fortran (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0

MPI软件 : MPICH  Version 3.2.1

软件版本 ：HPCG - 3.1 - March 28, 2019

### HPCG的环境配置

#### MPICH的安装和配置

参考链接：[MPICH并行开发环境安装 - Treasure_lee - 博客园](https://www.cnblogs.com/lijiaji/p/14284621.html)

##### 创建共享文件夹

参考链接：[虚拟机中Ubuntu与主机共享文件夹_ubuntu共享文件夹-CSDN博客](https://blog.csdn.net/qq_52783505/article/details/128720211?sharetype=blog&shareId=128720211&sharerefer=APP&sharesource=2401_87197351&sharefrom=qq)

一开始做的时候忘了ubuntu好像也能下载，就光想着做共享文件夹了

<img src="https://github.com/SUMMMERRR/markdown/blob/master/image-20241224105255077.png?raw=true" alt="image-20241224105255077.png" style="zoom:50%;" />

打开共享文件夹也安装了vmtools，但是mnt下始终没有，输入以下命令：

```shell
sudo vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other
```

挂载成功！

##### MPICH的安装

首先安装必要环境：

```shell
sudo apt-get install gcc
sudo apt-get install g++
sudo apt-get install cmake
sudo apt-get install gfortran
```

开始安装MPICH：

```shell
./configure -prefix=/home/ubuntu/software/mpich
完毕以后sudo make 开始编译
编译完毕以后开始安装 sudo make install
```

没报错就是成功了，~~虽然我实际安装的时候报了巨多的错~~，这是回忆着写的，中间的不少细节已经忘了🫥

##### MPICH配置环境变量

```shell
vim ~/.bashrc
```

一直拉到最后，插入三句话

```shell
export MPI_ROOT=/home/ubuntu/software/mpich
export PATH=$MPI_ROOT/bin:$PATH
export MANPATH=$MPI_ROOT/man:$MANPATH
```

保存退出，然后刷新一下：

```shell
source  ~/.bashrc
```

检测有没有添加成功：

```shell
which mpicc
which mpiexec
which mpif90
```

![image-20241224111746067.png](https://github.com/SUMMMERRR/markdown/blob/master/image-20241224111746067.png?raw=true)

再运行一下exp试试

```shell
mpirun -np 10 ./examples/cpi
mpiexec -n 4 ./examples/cpi
```

结果：

![image-20241224112122083.png](https://github.com/SUMMMERRR/markdown/blob/master/image-20241224112122083.png?raw=true)



Done！🥰

附上版本号（有点老了虽然但是）：

<img src="C:\Users\starr\AppData\Roaming\Typora\typora-user-images\image-20241224162231369.png" alt="image-20241224162231369" style="zoom:50%;" />

**期间遇到的问题：**

在执行./configure时，报错：

> configure: error: The Fortran compiler gfortran will not compile files that call the same routine with arguments of different types.

输入以下命令：

```shell
export PATH="~/work/x/bin:$PATH"
 
export FFLAGS="-w -fallow-argument-mismatch -O2"
```

再次执行./configure就完美解决了！~~另外提一句，CSDN的重复性也太高了吧，全是相同的内容~~

参照：

>   Alessio 2020-05-13 12:57:19 UTC
>
>   Steve workaround also worked for me (Fedora 32 + MPICH).
>   I just had to replace setenv with export as follows:
>   export PATH="~/work/x/bin:$PATH"   
>   export FFLAGS="-w -fallow-argument-mismatch -O2"
>
>   Hope this helps.
>
>   参考链接：[91731 – Configure error on building MPICH](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=91731#c8)
>
>   

#### HPCG的安装和配置

参考链接：[HPCG测试 - Treasure_lee - 博客园](https://www.cnblogs.com/lijiaji/p/14283958.html)

下载HPCG，这里使用 git clone 简单方便一点

```shell
git clone https://github.com/hpcg-benchmark/hpcg.git
```

之后修改配置文件：

```shell
vim Make.Linux_MPI
```

修改：

> TOPdir       = ./home/ubuntu/software/hpcg/hpcg
>
> MPdir        =/home/ubuntu/software/mpich
> MPinc        =-I\$(MPdir)/include
> MPlib        =$(MPdir)/lib/libmpi.a
>
> CXX          = /home/ubuntu/software/mpich/bin/mpicxx

修改完毕以后在setup目录下创建build文件夹，进入后设置安装目录：



> /home/ubuntu/software/hpcg/hpcg/configure Linux_MPI

开始安装：make

安装完毕以后在bin目录下会出现两个文件，hpcg.dat和xhpcg可执行程序.修改hpcg文件，将其最后一行改成1800s，hpcg规定至少运行1800s才能得出结果。

运行测试：

```shell
mpirun -np 8 ./xhpcg
```

跑了大概二三十分钟，最后生成文本 HPCG-Benchmark_3.1_2024-12-24_17-16-02.txt 

问题规模

> 104 104 104
> 1800

运行结果：

> Final Summary=
> Final Summary::HPCG result is VALID with a GFLOP/s rating of=5.99649
> Final Summary::HPCG 2.4 rating for historical reasons is=6.43646
> Final Summary::Reference version of ComputeDotProduct used=Performance results are most likely suboptimal
> Final Summary::Reference version of ComputeSPMV used=Performance results are most likely suboptimal
> Final Summary::Reference version of ComputeMG used=Performance results are most likely suboptimal
> Final Summary::Reference version of ComputeWAXPBY used=Performance results are most likely suboptimal
> Final Summary::Results are valid but execution time (sec) is=1791.46
> Final Summary::Official results execution time (sec) must be at least=1800



### 相关源代码分析

#### 稀疏矩阵的压缩存储CSR

在一个稀疏矩阵中，大部分的元素都是0。例如：

A = $$\begin{pmatrix}
1.5&0&0\\
0&2.0&0\\
0&0&5.0\\
\end{pmatrix}$$

在这个矩阵中，非零元素有3个，零有6个。

存储矩阵的一般方法是采用二维数组，其优点是可以随机地访问每一个元素，因而能够较容易地实现矩阵的各种运算，如转置运算、加法运算、乘法运算等。

对于稀疏矩阵来说，采用二维数组的存储方法既浪费大量的存储单元用来存放零元素，又要在运算中花费大量的时间来进行零元素的无效计算。所以必须考虑对稀疏矩阵进行压缩存储。

在HPCG中，采用的是CSR存储，其存储方法为：

![图片来自网络](https://i-blog.csdnimg.cn/blog_migrate/8b02178accd1c65db6884b752d310fdd.png)

这里的values是矩阵中所有非零值形成的数组

这里的column indices指的是values中的对应值在**列**的下标。例如在图片里的1，在矩阵的第0列，所以对应0。相似的，7在第1列，对应1。

row offsets共包含矩阵总行数+1个值。最后一个数指的是矩阵中非零数的个数，比如这个矩阵里面有 9 个非零数，row offsets 的最后一个数就是 9。

前 rows 个数指每一行第一个非零数在values中的偏移量。

所谓的偏移量也很好理解，行偏移表示某一行的第一个元素在values里面的起始偏移位置

例如矩阵第 0 行中，1 是该行第一个非零数，它在 values 中是第 0 个数。因此，第 0 行的 row offsets 为 0 ；同理，矩阵第 1 行中，2 是该行的第一个非零数，2 在 values 中是第 2 个数，则第 1 行对应的 row offsets 值为 2。之后的几个数以此类推。

对应到HPCG的代码部分：

代码文件：src/SparseMatrix.hpp（部分）

```cpp
struct SparseMatrix_STRUCT {
  char  * title; //!< 稀疏矩阵的名称
  global_int_t totalNumberOfRows; //!< 所有进程中的矩阵行总数
  global_int_t totalNumberOfNonzeros; //!< 所有进程中矩阵非零的总数
  local_int_t localNumberOfRows; //!< 一个进程中的行总数
  local_int_t localNumberOfColumns;  //!<一个进程中的列总数
  local_int_t localNumberOfNonzeros;  //!< 本地进程中的非零总数
   char  * nonzerosInRow;  //!< 一行中非零的数量将始终为27或更少
  global_int_t ** mtxIndG; //!< 作为全局变量的矩阵索引
  local_int_t ** mtxIndL; //!< 作为局部变量的矩阵索引
  double ** matrixValues; 
  double ** matrixDiagonal;
  }
```
以下是数据结构：
```cpp
inline void InitializeSparseMatrix(SparseMatrix & A, Geometry * geom) {
  A.title = 0;
  A.geom = geom;
  A.totalNumberOfRows = 0;
  A.totalNumberOfNonzeros = 0;
  A.localNumberOfRows = 0;
  A.localNumberOfColumns = 0;
  A.localNumberOfNonzeros = 0;
  A.nonzerosInRow = 0;
  A.mtxIndG = 0;
  A.mtxIndL = 0;
  A.matrixValues = 0;
  A.matrixDiagonal = 0;
  }
```

对于随意的一个矩阵来说

A = $$\begin{pmatrix}
1.5&0&0\\
0&2.0&3.0\\
4.0&0&5.0\\
\end{pmatrix}$$

对应的CSR存储：

- `nonzerosInRow = [1, 2, 2]`
- `matrixValues[0] = [1.5]`，`matrixValues[1] = [2.0, 3.0]`，`matrixValues[2] = [4.0, 5.0]`
- `mtxIndL[0] = [0]`，`mtxIndL[1] = [1, 2]`，`mtxIndL[2] = [0, 2]`

#### 矩阵向量乘法（SpMV）

这里的矩阵向量乘法实际上是要 计算 $$y=A*x$$，解出其中的x。

$$ y_i=\sum\limits_{j=0}^{n-1}A_{i,j}x_j  $$

代码位置：src/ComputeSPMV_ref.cpp(部分)
```cpp
int ComputeSPMV(const SparseMatrix &A, Vector &x, Vector &y) {
//按行扫描开始遍历
  const double * const xv = x.values;  // 输入向量x的数组指针
  double * const yv = y.values;  // 输出向量y的数组指针
  for (local_int_t i = 0; i < A.localNumberOfRows; i++) {
    double sum = 0.0;
    for (int j = 0; j < A.nonzerosInRow[i]; ++j) {
      // 取第i行的第j个非零元素的值和列索引
      double val = A.matrixValues[i][j];
      int col = A.mtxIndL[i][j];
      sum += val * x.values[col]; // 累加 A[i][col] * x[col]
    }
    y.values[i] = sum; // 计算结果写入y的第i个位置
  }
  return 0;
}
```

####  共轭梯度法（CG）

CG是一种迭代法，用于求解对称正定矩阵 $$A⋅x=b$$。其核心思想是通过迭代找到一组共轭的搜索方向，逐步逼近精确解。

代码位置：src/CG.cpp(部分)

```cpp
int CG(const SparseMatrix &A, Vector &b, Vector &x) {
  Vector r = b - A*x;  // 初始残差
  Vector p = r;        // 初始搜索方向
  for (int iter = 0; iter < max_iter; ++iter) {
    double alpha = (r.dot(r)) / (p.dot(A*p)); // 计算步长
    x = x + alpha * p;                       // 更新解
    Vector r_new = r - alpha * (A*p);        // 更新残差
    double beta = (r_new.dot(r_new)) / (r.dot(r));
    p = r_new + beta * p;                    // 更新搜索方向
    r = r_new;
  }
  return 0;
}
```
假设： $$A= \begin{bmatrix}
4&1\\
1&3\\
\end{bmatrix}$$  $$b=\begin{bmatrix}
1&2
\end{bmatrix}$$ ，初始猜测$$x=\begin{bmatrix}
0&0
\end{bmatrix} $$

1. 第一次迭代：计算 α=0.22*α*=0.22，更新 x=[0.22,0.44]*x*=[0.22,0.44]
2. 第二次迭代：计算 α=0.18*α*=0.18，更新 x=[0.45,0.62]*x*=[0.45,0.62]
3. 逐步逼近精确解 x=[0.2,0.6]*x*=[0.2,0.6]

#### 对称高斯-赛德尔预处理（SYMGS）

SYMGS是一种迭代方法，用于近似求解 $$ M*z=r $$ 的下三角部分。其公式为：

![image-20250216135022900.png](https://github.com/SUMMMERRR/markdown/blob/master/image-20250216135022900.png?raw=true)

**资料来源网络**

代码位置：src/ComputeSYMGS_ref.cpp(部分)

```cpp
int ComputeSYMGS(const SparseMatrix &A, const Vector &r, Vector &x) {
  const double *rv = r.values;  // 残差向量r的数组指针
  double *xv = x.values;        // 解向量x的数组指针

  // 前向扫描（从上到下更新x）
  for (local_int_t i = 0; i < A.localNumberOfRows; ++i) {
    double sum = rv[i];  // 初始化为残差r[i]
    const double *curVal = A.matrixValues[i];
    const local_int_t *curInd = A.mtxIndL[i];

    // 遍历当前行的非零元素（跳过对角元素）
    for (int j = 0; j < A.nonzerosInRow[i]; ++j) {
      local_int_t col = curInd[j];
      if (col != i) {  // 跳过对角元素（已经在残差中）
        sum -= curVal[j] * xv[col]; // 减去非对角元素的贡献
      }
    }
    xv[i] += sum / A.matrixDiagonal[i]; // 更新x[i]
  }

  // 后向扫描（从下到上再次更新x，代码类似但逆序循环）
  for (local_int_t i = A.localNumberOfRows - 1; i >= 0; --i) {
    // ... 类似前向扫描，但使用已更新的x值 ...
  }
  return 0;
}
```
假设$$ A=\begin{pmatrix}
4&1\\
1&3\\
\end{pmatrix}$$ $$r=\begin{pmatrix} 5&7\ \end{pmatrix}$$ ,初始$$x=\begin{pmatrix} 0&0\ \end{pmatrix}$$ ：

1. **前向扫描**：
   - 更新$$x_0 =(5-0)/4 = 1.25$$
   - 更新 $$x1= (7-1*1.25)/3 = 1.92$$
2. **后向扫描**：
   - 更新 $$x1=(7-0)/3 = 2.33$$（假设反向计算）

#### 多网格预处理（MG）

高频误差：局部剧烈变化的分量，可通过局部光滑（如SYMGS）快速消除。

低频误差：全局平滑的分量，需在粗网格上解决。

```cpp
// 代码位置：src/MG.cpp
void MG(SparseMatrix &A, Vector &b, Vector &x) {
  // 1. 前光滑（SYMGS）
  ComputeSYMGS(A, b, x);

  // 2. 计算残差并限制到粗网格
  Vector residual = ComputeResidual(A, b, x);
  Vector residual_coarse = Restrict(residual);

  // 3. 递归求解粗网格问题
  Vector e_coarse = MG(A_coarse, residual_coarse);

  // 4. 延拓修正量并更新解
  Vector e = Prolongate(e_coarse);
  x = x + e;

  // 5. 后光滑（SYMGS）
  ComputeSYMGS(A, b, x);
}
```
假设细网格有1000个点，粗网格有100个点：

在细网格上做SYMGS，消除局部误差。

将残差转移到粗网格求解。

将粗网格的解插值回细网格修正。

再次光滑消除插值引入的高频误差。


### 可能的优化方案

基于AMD霄龙CPU架构的优化：

>HPCG 使用 mpirun 以 MPI 或 MPI+OpenMP 应用程序的标准方式运行。如果您构建了支持 OpenMP 的 HPCG（建议使用以获得最佳性能），则应使用适当的映射启动它，以确保每个 MPI 列的 OpenMP 线程不会跨越不同的 CCX（在 AMD EPYC™ CPU 中，CCX 是一组共享 L3 缓存和其他内存硬件的内核）， 因为这会降低性能。系统还应在配置 SMT （硬件多线程） 的情况下关闭。

>在 OpenMPI 中，这是使用 map-by 语句实现的。例如，要在 AMD EPYC™ Gen 2 或更高版本的 CPU 上运行每个列 2 个 OpenMP 线程的 HPCG：

>适用于 AMD EPYC™ 处理器的运行脚本


```shell
#!/bin/bash
# Loading HPCG built with AOCC
spack load hpcg %aocc

# Group of cores which share an L3 cache (or CCX) is 8 for most EPYC Gen 2-5 CPUs, 4 for EPYC Gen 1
# For frequency optimised "F-parts", check documentation
CORES_PER_L3CACHE=8
NUM_CORES=$(nproc)

# OpenMP Settings
export OMP_PROC_BIND=true
export OMP_PLACES=cores
export OMP_NUM_THREADS=2

# MPI settings
MPI_RANKS=$(( $NUM_CORES / $OMP_NUM_THREADS ))
RANKS_PER_L3CACHE=$(( $CORES_PER_L3CACHE / $OMP_NUM_THREADS ))
MPI_OPTS=“-np $MPI_RANKS --bind-to core --map-by ppr:$RANKS_PER_L3CACHE:l3cache:pe=$OMP_NUM_THREADS ”

# Run HPCG
mpirun $MPI_OPTS xhpcg
```

基于intel至强CPU的优化方案：

[Intel China HPC Practice Book-1212 Update](https://www.intel.cn/content/dam/www/central-libraries/cn/zh/documents/2023-12/23-cmf157-intel-china-hpc-practice-book-v3-brochure.pdf)




## 参考资料

[HPCG - 上海交大超算平台用户手册](https://docs.hpc.sjtu.edu.cn/app/benchtools/hpcg.html)

[HPCG Benchmark](https://hpcg-benchmark.org/)

[HPCG |AMD公司](https://www.amd.com/en/developer/zen-software-studio/applications/spack/hpcg-benchmark.html)

[GitHub - hpcg-benchmark/hpcg: Official HPCG benchmark source code](https://github.com/hpcg-benchmark/hpcg)

[MPICH并行开发环境安装 - Treasure_lee - 博客园](https://www.cnblogs.com/lijiaji/p/14284621.html)

[91731 – Configure error on building MPICH](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=91731#c8)

[HPCG测试 - Treasure_lee - 博客园](https://www.cnblogs.com/lijiaji/p/14283958.html)

[Linpack之HPCG测试（HPCG Benchmark）-CSDN博客](https://blog.csdn.net/weixin_43014927/article/details/108671635)

[稀疏矩阵存储与优化-CSDN博客](https://blog.csdn.net/yhb1047818384/article/details/78996906)

[稀疏矩阵存储与优化-CSDN博客](https://blog.csdn.net/yhb1047818384/article/details/78996906)

[稀疏矩阵（科学名词）_百度百科](https://baike.baidu.com/item/稀疏矩阵/3249303)

[共轭梯度法简介 - 知乎](https://zhuanlan.zhihu.com/p/178461470)

[无痛版共轭梯度法介绍(更新到第五章) - 知乎](https://zhuanlan.zhihu.com/p/234950550)

https://enigmahuang.me/files/old-blog-archive/2017-HPCG_3_reference_implementation_%E9%98%85%E8%AF%BB%E7%AC%94%E8%AE%B0.pdf

https://enigmahuang.me/2017/12/27/HPCG-3-notes/www.netlib.org/utk/people/JackDongarra/SLIDES/hpcg-isc-0614.pdf

https://www.hpcg-benchmark.org/downloads/sc15/HPCG-Workshop-TH2.pdf