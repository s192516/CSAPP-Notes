## P291-295

对信号重新排列和拍照
DEMW是流水线寄存器，fdemw流水线阶段。在PIPE-中，会在流水线一直携带这些信号穿过执行和访存阶段，直到写回阶段才送到寄存器文件。确保写端口的地址和输入是来自同一条指令。否则会将处于写回阶段的指令的值写入，而寄存器ID却来自处于译码阶段的指令。

流水线化设计的目的就是每个时钟周期都发射一条新指令，必须在取出当前指令之后，马上确定下一条指令的位置。通过预测PC的下一个值，大多情况下可以达到每个时钟周期发射一条新指令的目的。

控制相关、计算相关，这些相关可能会导致流水线产生冒险，数据冒险和控制冒险。
