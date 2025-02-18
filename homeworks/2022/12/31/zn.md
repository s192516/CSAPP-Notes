**第7章 链接**

链接在以下**三个阶段**都可以执行：

1. **编译时**，即在源代码被翻译成机器代码时
2. **加载时**，即程序被加载器加载到内存并执行时
3. **运行时**，即由应用程序来执行

现代系统中，链接是由**链接器**自动执行的。

链接器使**分离编译**成为可能。

**7.1 编译器驱动程序**

**编译器驱动程序**可以使用户根据需要调用**语言预处理器**、**编译器**、**汇编器**和**链接器**。

通过**静态链接**，链接器将多个**可重定位目标文件**组合形成一个**可执行目标文件**。

**7.2 静态链接**

**静态链接器**以**一组可重定位目标文件**和**命令行参数**作为输入，生成一个**完全链接**的、**可以加载和运行**的**可执行目标文件**作为输出。

在构造可执行文件的过程中，链接器主要完成两个任务：

1. **符号解析**

2. 1. **什么是符号：**目标文件**定义**和**引用**符号，每个符号对应着一个**函数**、**全局变量**或**静态变量**
   2. **符号解析的**目的**：将每个**符号引用**和一个**符号定义关联起来

3. **重定位**

4. 1. 由编译器和汇编器生成的**可重定位目标文件**中的代码和数据节是从 0 开始的。可重定位目标文件中还包含重定位条目。
   2. **如何实现重定位**：链接器通过把每个**符号定义**和一个**内存位置（运行时地址）关联起来**以实现重定位。然后修改所有对这些符号的**引用**，使它们指向这个内存位置。

**7.3 目标文件**

一个目标文件又称**目标模块**。目标文件纯粹是**字节块的集合**。目标文件本身是一个字节序列。

这些字节块中有些包含**程序代码**或**程序数据**，其他的则包含**引导链接器和加载器的数据结构**。

**链接器**把这些块连接起来，确定被连接块的运行时位置，并修改代码和数据块中的各种位置。

**目标文件有三种形式：**

1. **可重定位目标文件**：包含二进制的**代码**和**数据**。可以与其他可重定位目标文件合并成可执行目标文件。又称 obj 文件，gcc 经过预处理、编译、汇编后生成的 .o 文件即为可重定位目标文件。

2. **可执行目标文件：**包含二进制的代码和数据。可以被直接复制到内存并执行。简称可执行文件，gcc 经过链接后生成的 .out 文件以及无后缀名文件都是可执行文件。

3. 1. 在 linux 中，.out 文件和无后缀名文件基本意义一样，只是命名习惯的不一致而已，即 main.out 和 main 两个文件是一样的。

4. **共享目标文件**：特殊类型的可重定位目标文件，即**动态链接库**。可以在**加载**或者**运行时**被动态地加载进内存并链接。

**静态链接库属于哪种呢？**

编译器和汇编器生成可重定位目标文件和**共享目标文件**，链接器生成可执行目标文件。

目标文件是按照特定的目标文件格式来组织的，各个系统的目标文件格式都不相同。Windows 使用 **PE** 格式，Linux 使用 **ELF** 格式。

**7.4 可重定位目标文件**

本书以 Linux 中的 **ELF 可重定位目标文件**为例。

可重定位目标文件由**多个不同的节**组成，每一节都是一个连续的字节序列。指令、初始化了的全局变量、未初始化的的变量分别位于不同的节。

**一个 ELF 可重定位文件中包含以下节（按位置顺序排列）：**

1. **ELF 头**：特殊的节，包含文件的一些基本属性信息，用来**解释目标文件**和**帮助链接器进行语法分析**。

2. 1. **包含内容**：生成该文件的系统的字的大小和字节顺序，ELF 头的大小，目标文件的类型，机器类型(如 x86-64)，节头部表的文件偏移，节头部表中条目的大小和数量。

3. **.text：**包含已编译程序的机器代码。即存放的是指令代码。

4. **.rodata：**包含一些特殊的只读数据。

5. **.data：**包含已初始化的全局和静态变量。

6. **.bbs：**包含未初始化的全局和静态变量，以及所有被初始化为 0 的全局或静态变量。

7. 1. **注意：**.bss 节在目标文件中仅是一个占位符，不占据实际空间。这两类变量都是运行时在内存中为其分配变量，并初始化为 0

8. **.symtab：**包含一个**符号表**。存放了在程序中**定义**和**引用**的**符号 (即函数和全局变量)** 的信息。

9. 1. **注意：**与可编译器中的符号表不同，.symtab 中的符号表不包含局部变量的条目。

10. **.rel.text：**包含一个 .text 节中位置的列表，当链接器把此目标文件与其他文件组合时，需要修改这些位置。

11. 1. 一般**任何调用外部函数或引用全局变量的指令都需要修改**，而调用本地函数的指令则不需要修改。**为什么不需要修改呢？**
    2. **注意：**可执行目标文件不需要重定位，一般不包含 .rel.text 和 .rel.data 节。
    3. **理解****：**.rel.text 中包含的实际上是**代码的重定位条目**。

12. **.rel.data：**包含被模块引用或定义的所有全局变量的重定位信息。

13. 1. 如果一个已初始化的全局变量其初始值是一个全局变量地址或外部定义函数的地址，就需要被修改。
    2. **理解**：.rel.data 中包含的实际上是**已初始化的数据的重定位条目**。

14. **.debug**：一个**调试符号表**，内部包含的条目是程序中定义的局部变量和类型定义，程序中定义和引用的全局变量，还有原始的 C 源文件。

15. 1. **注意：**.debug 节并不总是存在，只有用 -g 选项来调用编译器驱动程序时，才会有这一节。

16. **.line：**包含原始 C 源程序中的**行号**和 .text 节中**机器指令**之间的**映射**。

17. 1. **注意：**.line 节和 .debug 节一样，并不总是存在，只有用 -g 选项来调用编译器驱动程序时，才会有这一节。

18. **.strtab：**包含一个**字符串表**，其中包括 .symtab 和 .debug 节中的符号表，以及节头部中的节名字。

19. 1. 字符串表实际上就是一个以 null 结尾的**字符串的序列**。

20. **节头部表：**特殊的节，是一个用来描述目标文件的节。

21. 1. 内容：含有与目标文件中每个节相对应的一个**条目**，描述了对应节的位置和大小等信息。

注意局部变量在**运行时保存在栈中**，既不出现在 .data 节中，也不出现在 .bss 节中。