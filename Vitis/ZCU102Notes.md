# ZCU102应用笔记

## 启动模式及连接
1. 首先设置[拨码开关(Section2--MPSoC Device Configuration)](https://china.xilinx.com/support/documentation/boards_and_kits/zcu102/ug1182-zcu102-eval-bd.pdf)

    Boot Mode|Mode Pins[3:0]|Mode SW6[4:1]
    -|-|-
    JTAG  |0 0 0 0|on, on, on, on
    QSPI32|0 0 1 0|on, on, off,on
    SD    |1 1 1 0|off,off,off,on

    选择SD卡启动模式，并将Vitis编译生成的SD卡文件拷贝到SD卡，插电，开机，之后就可以通过PC来远程控制ZCU102了
2. 有两种方式可以连接开发板：
    * 通过ssh连接，**用户名和密码**均为root，Windows系统下可以使用MobaXterm进行链接，Ubuntu下可以使用ssh连接
        ```bash
        ssh root@192.168.3.11
        ```
    * 通过串口连接，波特率为115200，Windows下用MobaXterm连接很好用，ubuntu下用minicom（不太熟悉）

## Debug