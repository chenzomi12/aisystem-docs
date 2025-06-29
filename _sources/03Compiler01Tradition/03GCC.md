<!--Copyright © ZOMI 适用于[License](https://github.com/chenzomi12/AISystem)版权许可-->

# GCC 主要特征

GCC（GNU Compiler Collection，GNU 编译器集合）最初是作为 GNU 操作系统的编译器编写的，旨在为 GNU/Linux 系统开发一个高效的 C 编译器。其历史可以追溯到 1987 年，当时由理查德·斯托曼（Richard Stallman）创建，作为 GNU 项目的一部分。

最初，GCC 仅是一个用于编译 C 语言的编译器，但很快扩展以支持其他编程语言，其设计强调可移植性和模块化，使其能够适应多种硬件和操作系统环境。

在 1990 年代和 2000 年代，GCC 经历了几次重要的重构和扩展。改进包括引入新的优化技术、提升代码生成和分析能力，以及增强对新兴编程语言和硬件架构的支持。这些改进使得 GCC 成为编程社区中的重要工具，广泛应用于学术研究、商业开发和开源项目中。随着 GNU/Linux 系统的快速发展，GCC 逐渐扩展其支持范围，涵盖了包括 Microsoft Windows、BSD 系列、Solaris 和 Mac OS 在内的多种操作系统平台。

同时，GCC 的指导委员会根据其使命宣言做出重要决策，致力于持续发布高质量的版本。通过 Git 版本控制系统和每周的源代码快照，GCC 的最新源代码随时可供获取。任何人都被鼓励参与贡献或协助测试，以推动 GCC 的持续发展。

![编译器](../images/03Compiler01Tradition/03GCC01.png)  

此外，GCC 还引入了与现代编程语言如 Swift 和 Java 相关的前端，使其成为一个全面而多功能的编译器。作为一个模块化设计的软件，GCC 提供了丰富的功能和灵活性，既能在本地平台上进行编译，也支持跨平台的交叉编译。作为自由软件的一部分，用户可以免费获取并自由使用 GCC，并且得到了自由软件基金会（Free Software Foundation，FSF）的强力支持。

GCC 具有以下主要特征：

- 可移植性：支持多种硬件平台，使得用户可以在不同的硬件架构上进行编译。
- 跨平台交叉编译：支持在一个平台上为另一个平台生成可执行文件，这对嵌入式开发尤为重要。
- 多语言前端：除了 C 语言，还支持 C++、Objective-C、Fortran、Ada、Go 和 D 等多种编程语言。
- 模块化设计：允许轻松添加新语言和 CPU 架构的支持，增强了扩展性。
- 开源自由软件：源代码公开，用户可以自由使用、修改和分发。

## GCC 编译流程

GCC 的编译过程可以大致分为预处理、编译、汇编和链接四个阶段。

![编译器](../images/03Compiler01Tradition/03GCC02.png)

### 源程序(文本)

当编写源程序时，通常会使用以 .c 或 .cpp 为扩展名的文件。下面以打印宏定义 HELLOWORD 为例，我们使用 C 语言编写 hello.c 源文件：  

```c
#include <stdio.h>

#define HELLOWORD ("hello world\n")

int main(void){
    printf(HELLOWORD);
    return 0;
}
```

### 预处理(cpp)

生成文件 hello.i 

```shell
gcc -E hello.c -o hello.i
```

在预处理过程中，源代码会被读入，并检查其中包含的预处理指令和宏定义，然后进行相应的替换操作。此外，预处理过程还会删除程序中的注释和多余空白字符。最终生成的.i 文件包含了经过预处理后的代码内容。 

当高级语言代码经过预处理生成.i 文件时，预处理过程会涉及宏替换、条件编译等操作。以下是对这些预处理操作的解释：

- 头文件展开：

    在预处理阶段，编译器会将源文件中包含的头文件内容插入到源文件中对应的位置，以便在编译时能够访问头文件中定义的函数、变量、宏等内容。

- 宏替换：

    在预处理阶段，编译器会将源文件中定义的宏在使用时进行替换，即将宏名称替换为其定义的内容。这样可以简化代码编写，提高代码的可读性和可维护性。

- 条件编译：

    通过预处理指令如 `#if`、`#else`、`#ifdef` 等，在编译前确定某些代码片段是否应被包含在最终的编译过程中。这样可以根据条件编译选择性地包含代码，实现不同平台、环境下的代码控制。

- 删除注释：

    在预处理阶段，编译器会删除源文件中的注释，包括单行注释（//）和多行注释（/.../），这样可以提高编译速度并减少编译后代码的大小。

- 添加行号和文件名标识：

    通过预处理指令如 `#line`，在预处理阶段添加行号和文件名标识到源文件中，便于在编译过程中定位错误信息和调试。

- 保留 `#pragma` 命令：

    在预处理阶段，编译器会保留以 `#pragma` 开头的预处理指令，如 `#pragma once`、`#pragma pack` 等，这些指令可以用来指导编译器进行特定的处理，如控制编译器的行为或优化代码。

`hello.i` 文件部分内容如下，详细可见 `../code/gcc/hello.i` 文件。

```c
int main(void){
    printf(("hello world\n"));
    return 0;
}
```

在该文件中，已经将头文件包含进来，宏定义 `HELLOWORD` 替换为字符串 `"hello world\n"`，并删除了注释和多余空白字符。

### 编译(ccl)

在这里，编译并不仅仅指将程序从源文件转换为二进制文件的整个过程，而是特指将经过预处理的文件（`hello.i`）转换为特定汇编代码文件（`hello.s`）的过程。

在这个过程中，经过预处理后的 `.i` 文件作为输入，通过编译器（ccl）生成相应的汇编代码 `.s` 文件。编译器（ccl）是 GCC 的前端，其主要功能是将经过预处理的代码转换为汇编代码。编译阶段会对预处理后的 `.i` 文件进行语法分析、词法分析以及各种优化，最终生成对应的汇编代码。

汇编代码是以文本形式存在的程序代码，接着经过编译生成 `.s` 文件，是连接程序员编写的高级语言代码与计算机硬件之间的桥梁。

生成文件 `hello.s`：

```shell
gcc -S hello.i -o hello.s
```

打开 `hello.s` 后输出如下：

```
    .section    __TEXT,__text,regular,pure_instructions
    .build_version macos, 10, 15    sdk_version 10, 15, 6
    .globl    _main                   ## -- Begin function main
    .p2align    4, 0x90
_main:                                  ## @main
    .cfi_startproc
## %bb.0:
    pushq    %rbp
    .cfi_def_cfa_offset 16
    .cfi_offset %rbp, -16
    movq    %rsp, %rbp
    .cfi_def_cfa_register %rbp
    subq    $16, %rsp
    movl    $0, -4(%rbp)
    leaq    L_.str(%rip), %rdi
    movb    $0, %al
    callq    _printf
    xorl    %ecx, %ecx
    movl    %eax, -8(%rbp)          ## 4-byte Spill
    movl    %ecx, %eax
    addq    $16, %rsp
    popq    %rbp
    retq
    .cfi_endproc
                                        ## -- End function
    .section    __TEXT,__cstring,cstring_literals
L_.str:                                 ## @.str
    .asciz    "hello world\n"

.subsections_via_symbols
```

现在 `hello.s` 文件中包含了完全是汇编指令的内容，表明 `hello.c` 文件已经被成功编译成了汇编语言。

### 汇编(as)

在这一步中，我们将汇编代码转换成机器指令。这一步是通过汇编器(as)完成的。汇编器是 GCC 的后端，其主要功能是将汇编代码转换成机器指令。

汇编器的工作是将人类可读的汇编代码转换为机器指令或二进制码，生成一个可重定位的目标程序，通常以 `.o` 作为文件扩展名。这个目标文件包含了逐行转换后的机器码，以二进制形式存储。这种可重定位的目标程序为后续的链接和执行提供了基础，使得汇编代码能够被计算机直接执行。

生成文件 `hello.o`

```shell
gcc -c hello.s -o hello.o
```

### 链接(ld)

链接过程中，链接器的作用是将目标文件与其他目标文件、库文件以及启动文件等进行链接，从而生成一个可执行文件。在链接的过程中，链接器会对符号进行解析、执行重定位、进行代码优化、确定空间布局，进行装载，并进行动态链接等操作。通过链接器的处理，将所有需要的依赖项打包成一个在特定平台可执行的目标程序，用户可以直接执行这个程序。

```shell
gcc -o hello.o -o hello
```

添加 `-v` 参数，可以查看详细的编译过程:

```shell
gcc -v hello.c -o hello
```

- 静态链接

静态链接是指在链接程序时，需要使用的每个库函数的一份拷贝被加入到可执行文件中。通过静态链接使用静态库进行链接，生成的程序包含程序运行所需要的全部库，可以直接运行。然而，静态链接生成的程序体积较大。

- 动态链接

动态链接是指可执行文件只包含文件名，让载入器在运行时能够寻找程序所需的函数库。通过动态链接使用动态链接库进行链接，生成的程序在执行时需要加载所需的动态库才能运行。相比静态链接，动态链接生成的程序体积较小，但是必须依赖所需的动态库，否则无法执行。

## GCC 编译方法

### 本地编译

所谓"本地编译"，是指编译源代码的平台和执行源代码编译后程序的平台是同一个平台。这里的平台，可以理解为 CPU 架构+操作系统。比如，在 Intel x86 架构或者 Windows 平台下、使用 Visual C++ 编译生成的可执行文件，在同样的 Intel x86 架构/Windows 10 下运行。

### 交叉编译

所谓"交叉编译（Cross_Compile）"，是指编译源代码的平台和执行源代码编译后程序的平台是两个不同的平台。比如，在 Intel x86 架构/Linux（Ubuntu）平台下、使用交叉编译工具链生成的可执行文件，在 ARM 架构/Linux 下运行。

## 与传统编译区别

传统的三段式划分是指将编译过程分为前端、优化、后端三个阶段，每个阶段都有专门的工具负责。

![编译器](../images/03Compiler01Tradition/03GCC03.png)

而在 GCC 中，编译过程被分成了预处理、编译、汇编、链接四个阶段 。其中 GCC 的预处理、编译阶段属于三段式划分的前端部分，汇编阶段属于三段式划分的后端部分。

GCC 的链接阶段是三段式划分后端部分的优化阶段合并，但其与后端部分的目的一致，都是为了生成可执行文件。

GCC 编译过程的四个阶段与传统的三段式划分的前端、优化、后端三个阶段有一定的重合和对应关系，但 GCC 更为详细和全面地划分了编译过程，使得每个阶段的功能更加明确和独立。

## 小结与思考

本节介绍了 GCC 的编译过程，主要包括预处理、编译、汇编和链接四个阶段。并总结了 GCC 的优点和缺点：

- GCC 优点
  - 支持多种编程语言：不仅支持 C，还扩展支持 C++、Fortran、Java、Ada 等，满足不同开发需求。
  - 跨平台支持广泛：在多种硬件架构和操作系统上运行，包括 x86、ARM、PowerPC 等，是跨平台应用开发的首选编译器。
  - 使用广泛且功能完备：在开源社区和商业开发中被广泛使用，提供丰富的优化和调试功能，适用于各种应用场景。
  - 基于 C 的架构：核心使用 C 语言编写，仅需 C++ 编译器即可编译，简化了编译环境的设置和维护。
- GCC 缺点
  - 代码耦合度高：内部模块间耦合度高，独立集成到专用 IDE 中较为困难，需要大量定制和修改。
  - 难以作为 API 集成：设计上未考虑作为 API 集成到其他工具中，使得与其他开发工具的无缝集成复杂。
  - 后期版本代码质量下降：随着功能扩展和代码量增加，部分版本的代码质量和维护性下降，增加了开发和调试难度。
  - 代码量庞大：代码库约 1500 万行，新开发者上手和理解项目较困难，增加了维护和开发成本。

## 本节视频

<html>
<iframe src="https://player.bilibili.com/player.html?aid=347765650&bvid=BV1LR4y1f7et&cid=896303807&p=1&as_wide=1&high_quality=1&danmaku=0&t=30&autoplay=0" width="100%" height="500" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
</html>
