# **HPCG**

12.24日终于把Ubuntu的网搞好了，之前没问题的，重启一次就连不上网了，nmcli device一直显示未托管，NAT模式和桥接模式也换了好几次，差点没气死我，总之最后终于是搞好了，感谢ChatGPT和CSDN。

对于HPCG是一无所知的，不过自己对于这方面算是感兴趣，也不烦捣鼓，通过这次作业增长一些见识和技能。

应该是会不定期摸鱼（

特别鸣谢ChatGPT和Microsoft Copilot AI

------



### 1.配置MPICH和测试HPCG

markdown文档的编辑使用了Typora这个软件，在CSDN上找到了了破解，还挺好使的（

参考资料：

- [HPCG测试 - Treasure_lee - 博客园](https://www.cnblogs.com/lijiaji/p/14283958.html)
- MPICH并行开发环境安装 - Treasure_lee - 博客园](https://www.cnblogs.com/lijiaji/p/14284621.html)
- 查看和修改PATH环境变量(Linux通用) - AR0X1V - 博客园](https://www.cnblogs.com/zhyantao/p/10424959.html)
- [Typora 免费安装教程（已支持最新版 1.9.5）_typora免费安装-CSDN博客](https://blog.csdn.net/qq_61621323/article/details/141036982)

刚开始配置MPICH的过程没有出现大的问题，参考大佬的文章一步步来还是十分顺利的，因为装了VMwareTools，让我虚拟机里的Ubuntu和Windows可以共享粘贴板，还能让虚拟机窗口自适应缩放与分辨率，方便了很多。

写这段东西的时候在等待漫长的make过程，已经make了20多分钟了，空气中十分宁静，希望不会出现问题。觉得Win11是狗屎，内存占用太大了，明天新内存就到了，从16G升级到32G爽爽爽。

联合编译完了，明天接着弄。

12.25摸了，但把新内存装上了。

12.26 把MPICH配置好了

![Cannedmygo/MPICH配置测试文件.png at main · Cannedpotatoes/Cannedmygo](https://github.com/Cannedpotatoes/Cannedmygo/blob/main/MPICH配置测试文件.png)

接下来就是安装HPCG，不算麻烦，网页里总结的很好，都看的懂，接下来是测试时间！

我的笔记本是2021款拯救者R7000P，CPU是 R7 5800H，内存为DDR4 32G 3200，给虚拟机分配了8G内存以及8个CPU核心，基准测试跑了大概半小时，最终成绩4.16GFLOP/s， 执行时间1735.18秒，接下来的任务就是围绕在提升GFLOP/s上和降低执行时间了，对我来说是有些难度的，之前没有接触过这些。

![Cannedmygo/全力以赴的CPU.jpg at main · Cannedpotatoes/Cannedmygo](https://github.com/Cannedpotatoes/Cannedmygo/blob/main/全力以赴的CPU.jpg)

首次测试结果↓

![Cannedmygo/12.26第一次测试结果.png at main · Cannedpotatoes/Cannedmygo](https://github.com/Cannedpotatoes/Cannedmygo/blob/main/12.26第一次测试结果.png)

### 2.程序主要功能

“HPCG (**High Performance Conjugate Gradient Benchmark**) 是一种用于测试高性能计算机实际性能的基准测试工具。它通过模拟科学计算中常见的稀疏线性代数操作来评估计算系统的性能，补充了传统基准测试（如 **HPL - 高性能 Linpack**）在某些方面的不足。”

------

HPCG程序的核心功能模块：

###### ①.初始化阶段

> 生成问题数据：
>
> > 创建稀疏对称正定矩阵A（模拟科学计算中的离散矩阵）。
> >
> > 初始化向量b和解向量x。
> >
> > 分布式环境下，矩阵和向量在多个节点间划分。
>
> MPI初始化：
>
> > 启动通信环境，确定计算规模、进程布局。

###### ②.性能优化阶段

> 性能调优：
>
> > 首先运行一小部分共轭梯度（Conjugate Gradient, CG）迭代。
> >
> > 记录每一步操作的执行时间。
> >
> > 估算基准测试所需的迭代次数（根据总运行时间和最差耗时）。

#此阶段的目的是确保在正式基准测试中能充分利用系统资源。

###### ③.基准测试阶段

> 稀疏矩阵操作：
>
> > 使用稀疏矩阵-向量乘法（SpMV）测试内存带宽的利用率。
> >
> > #这是 HPCG 的核心计算负载，因为 SpMV 是典型的带宽受限操作。

> 向量操作:
>
> > 包括向量加法、标量乘法、内积等，评估缓存性能和内存访问效率。

> 全局通信：
>
> > 分布式计算中需要对向量求全局归约（Reduction），例如计算内积的结果。
> >
> > #测试MPI的延迟和带宽性能。

> 迭代求解：
>
> > 共轭梯度法求解 A⋅x=b，包含：
> >
> > >矩阵-向量乘法。
> > >
> > >向量更新操作。
> > >
> > >预处理器（对角线 Jacobi 预处理）加速收敛。
> >
> > #强调迭代计算中系统的综合性能。

###### ④.验证与结果分析阶段

>结果正确性：
>
>> 比较计算解和精确解的差异，验证算法正确性。
>>
>> 计算残差||b-A⋅x||，确保收敛。

> 性能报告：
>
> 生成包括以下内容的报告：
>
> 1. 每秒浮点运算次数（FLOP/s）。
> 2. 稀疏矩阵-向量乘法性能。
> 3. 全局通信开销。
> 4. 内存带宽限制。
>
> #提供每节点和分布式性能的详细数据。

------

核心代码在src文件夹里的main.cpp里：

![Cannedmygo/main.png at main · Cannedpotatoes/Cannedmygo](https://github.com/Cannedpotatoes/Cannedmygo/blob/main/main.png)

其中的核心函数为CG函数，它是实现“共轭梯度法”的核心函数

以下是CG函数的源代码

![Cannedmygo/cg.png at main · Cannedpotatoes/Cannedmygo](https://github.com/Cannedpotatoes/Cannedmygo/blob/main/cg.png)

这段代码实现了共轭梯度（CG）算法，用于求解稀疏线性方程组。算法通过迭代更新解向量 `x` 和残差向量 `r`，直到满足收敛条件或达到最大迭代次数。在每次迭代中，计算预处理后的残差（如果有预处理），更新搜索方向 `p`，并通过矩阵向量乘法（SPMV）计算 `Ap`。接着，根据公式更新 `x` 和 `r`，并计算新的残差范数。如果残差相对初始值的比例小于给定容忍度，则算法停止。时间记录用于分析不同计算步骤的耗时。
