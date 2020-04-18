# [FPGA应用加速（ug1393）](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/kme1569523964461.html)
## [Building and Running the Application](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/xia1553473418160.html)
### [配置Vitis环境](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk2027126153.html#zks1565446519267)
### [编译选项](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk149388784.html#rst1525720251890)
* Software Emulation
* Hardware Emulation
* Hardware Execution
### [编译Host程序](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk1955329950.html#asy1528754332783)
1. 将host.cpp编译为.o文件
2. 将.o文件与XRT库链接
### [编译FPGA二进制文件](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk2086915788.html#tvy1528754367816)
![](./images/fpga_bin.png)
1. [将kernels编译成.xo文件](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk2086915788.html#ariaid-title2)
    ```bash
    v++ -t sw_emu --platform xilinx_u200_xdma_201830_2 -c -k vadd \
    -I'./src' -o'vadd.sw_emu.xo' ./src/vadd.cpp
    ```
    参数|备注
    -|-
    -t sw_emu|软件模拟
    --platform |选择平台，对于嵌入式平台--platform $PLATFORM_REPO_PATHS/zcu102_base/zcu102_base.xpfm
    -c|编译内核，编译(-c)和链接(-l)需要分为两个单独的步骤
    -k|kernel名称
    [**将RTL内核打包为package_xo**](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk2086915788.html#ariaid-title6)
2. [将.xo文件与硬件平台(.xsa)链接为FPGA二进制文件(.xclbin)](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk2086915788.html#ariaid-title7)
    ```bash
    v++ -t sw_emu --platform xilinx_u200_xdma_201830_2 --link vadd.sw_emu.xo \
    -o'vadd.sw_emu.xclbin' --config ./connectivity.cfg
    ```
    参数|备注
    -|-
    -t sw_emu|软件模拟
    --platform |选择平台，对于嵌入式平台--platform $PLATFORM_REPO_PATHS/zcu102_base/zcu102_base.xpfm
    --link|链接内核与平台生成xclbin文件
    --config ./connectivity.cfg|提供额外的注释
    **TODO学习:**
    * 2.1 Creating Multiple Instances of a Kernel
    * 2.2 Mapping Kernel Ports to Global Memory
    * 2.3 Specify Streaming Connections Between Compute Units
## [Run an Application](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk62438775.html#esv1560357060167)

### [运行仿真](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/Chunk62438775.html#esv1560357060167__section_jsy_g4d_s3b)
**Arm卡仿真极慢，建议使用x86卡仿真，不装x86卡，只配置好x86卡的环境也可以仿真**，对于Vitis所给例子，可以直接make test进行仿真，效果不错。
1. 编辑`xrt.ini`文件，如果使用**命令行**，`xrt.ini`文件需要手动创建在host程序同级目录下。这个文件主要控制在Debug时候需不需要打印波形图之类的，[详细信息](https://china.xilinx.com/html_docs/xilinx2019_2/vitis_doc/obl1532064985142.html)。以下是示例，
    ```
    #Start of Debug group 
    [Debug] 
    timeline_trace = true

    #Start of Runtime group 
    [Runtime] 
    runtime_log = console
    ```
2. 创建`emconfig.json`
    ```bash
    $emconfigutil --platform xilinx_u50_gen3x16_xdma_201920_3
    ```
3. 将环境变量`XCL_EMULATION_MODE`设置为`sw_emu`或者`hw_emu`
    ```bash
    export  XCL_EMULATION_MODE=sw_emu
    ```
4. 运行程序
    ```bash
    ./host.exe kernel.xclbin
    ```