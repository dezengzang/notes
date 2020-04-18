# [FPGA应用加速（ug1393）](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/kme1569523964461.html)
## [环境配置](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/vhc1571429852245.html)
环境配置文档在[Installation Requirements](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/aqm1532064088764.html)和[Vitis Software Platform Installation](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/dhg1543555360045.html)，主要包括：

1. 安装[OpenCL Installable Client Driver Loader](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/myg1532067628050.html)，它的作用是为Vitis选择OpenCL平台
2. [安装Vitis软件平台](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/juk1557377661419.html)
3. [安装XRT](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/pjr1542153622642.html)
4. [安装platform](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/rvu1542160683426.html)(类似与Intel的BSP)
5. [安装交叉编译环境Sysroot](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/rvu1542160683426.html)，它使用GNU Arm 交叉编译器编译host端代码
6. [设置环境变量](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/rbk1547656041291.html)

## [编译过程](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1289369954.html#ariaid-title5)
[Vitis Tutorials](https://github.com/Xilinx/Vitis-Tutorials)（包括如何将RTL代码打包为加速kernel）

[Vitis 应用加速实例](https://github.com/Xilinx/Vitis_Accel_Examples)（包含ZCU102的向量加法）

<!-- ## [使用Vitis加速应用的设计思路](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk438370504.html#wgb1568690490380) -->

## [编程模型](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1197588519.html#dxg1524542432285)
Vitis 开发套件支持基于OpenCL1.2的异构计算框架，host程序运行在x86或者Arm处理器上，并且将计算密集型的任务通过Xilinx Runtime(XRT)交给可编程逻辑（PL）端执行。(The Vitis core development kit supports heterogeneous computing using the industry standard OpenCL framework. The host program executes on the processor (x86 or Arm) and offloads compute intensive tasks through Xilinx Runtime (XRT) to execute on a hardware kernel running on programmable logic (PL) using the OpenCL programming paradigm)
### [内核特性](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1197588519.html#ariaid-title3)
1. 支持[多种语言](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1197588519.html#ariaid-title3)设计加速内核，如RTL，C，OpenCL

2. 内核执行模式，内核支持3种执行模式，每个内核都可以从以下的3种执行模式中选择一个。
![](./images/kernel_mode.png)

3. 内核接口类型，包括三种接口**Register,Memory-Mapped，Streaming**
    * [功能特性](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1197588519.html#stg1570636562399__section_jd5_f4n_3jb)
    ![](./images/interface_fun_prop.png)
    简单而言：**Register**类型用来从host向kernel单向传递标量；**Memory-Mapped**类型用作host和kernel之间双向传递数据，这块数据的基地址需要由host端用Register类型数据告诉kernel，随机存取，有延迟；**Streaming**用于内核与内核之间或者host与kernel之间的单向数据传递，顺序读取，不使用Global Memory，比Memory-Mapped延迟要低。
    * [实现要求](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1197588519.html#stg1570636562399__section_mrr_grn_3jb)
    ![](./images/interface_requirement.png)

4. 时钟和复位
![](./images/clk_rst.png)

## [Host应用](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk641954935.html#vpy1519742402284)
**Host应用遵循OpenCL1.2**，并且主要包括3部分：
1. Setting up the environment.
2. Core command execution including executing one or more kernels.
3. Post processing and release of resources.

以下各小节将对这三点进行详细说明
### [Setting Up the OpenCL Environment](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk641954935.html#ariaid-title2)

1. Platform
2. Devices
3. Context（**虽然OpenCL标准允许一个Context管理多个Device，但是XRT要求每个Device对应一个Context**）
4. Command Queues
5. Program
### [Executing Commands in the FPGA](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk641954935.html#ariaid-title9)

1. Setting up the kernels.
    1. 创建内核
    2. 设置内核参数(XRT希望创建完内核就设置参数)
2. Buffer transfer to/from the FPGA.
    * buffer要小于4GB
    * **建议使用clEnqueueMigrateMemObjects代替clEnqueueWriteBuffer和clEnqueueReadBuffer以提高性能**。连续执行同一个内核时能利用流水以提高效率。
3. Kernel execution on FPGA.
    
    有个概念很重要，感觉像多个kernel之间进行流水，**但是嵌入式平台不支持**[Host-to-kernel-dataflow](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1617283829.html#qon1555193786062)

4. Event synchronization.

## [C/C++ Kernels](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1617283829.html#rjk1519742919747)

**非常重要TODO**

## [Streaming Connections](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk308472235.html#qwz1555342848145)
**非常重要，用于kernel与kernel之间传输TODO**

## [Best Practices for Acceleration with Vitis](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/haf1532064985140.html)