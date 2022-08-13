# `g++`的使用方法

## 预处理、编译、汇编和链接

### 预处理
```bash
g++ -E test.cpp -o test.i
```
### 编译 
```bash
g++ -S test.i -o test.s
```
### 汇编
```bash
g++ -c test.s -o test.o
```
### 链接 
```bash
g++ test.o -o test
```

这里面的逻辑要思考一下

## `g++`重要编译参数

### `-g` 编译带调试信息的可执行文件
```bash
# -g 选项告诉GCC产生能被GNU调试器GDB使用的调试信息

g++ -g test.cpp -o test
```

### `-O[n]` 优化源代码
```bash
## 优化源代码。

# -O 告诉g++对源代码进行基本优化。
# -O0 表示不做优化
# -O1 表示默认优化
# -O2 表示完成O1优化之外，还进行一些额外的调整工作
# -O3 表示包括循环展开和其他一些预处理相关的特性工作

g++ -O2 test.cpp -o test
# 注意看看文件价的inefficiency的demo,时间差距有1000倍
```

### `-l`和`-L`制定库文件和库文件路径
```bash
# -l参数就是用来指定程序要链接的库，-l参数紧跟着就是库名
# 在`/lib, /usr/lib, /usr/local/lib`里的库直接用-l参数就能链接

# 链接glog库
g++ -lglog test.cpp

# 如果库文件没有在上面三个目录，则需要指定库文件所在目录# -L参数跟着库文件所在的目录名

# 链接mytest库，libmytest.so在/home/cfd-wy/Desktop/mytestlib目录下
g++ -L/home/cfd-wy/Desktop/mytestlib -lmytest test.cpp -o test
```

### `-I`指定头文件搜索目录
```bash
# -I
# /usr/include目录一般是不用指定的，gcc知道哪里找。但是如果头文件不再这个目录里面，我们就要用-I参数指定了。-I可以使用相对路径

g++ -I/myinclude test.cpp -o test
```

### `-Wall`打开警告信息
```bash
g++ -Wall test.cpp -o test
```

### `-w`关闭警告信息
```bash
g++ -w test.cpp -o test
```

### `-std=c++11``设置编译标准
```bash
g++ -std=c++11 test.cpp -o test
```

### `-o`制定输出文件名


### `-D`定义宏
```bash
# 在使用gcc/g++编译时定义宏

# 常用场景

# -DDEBUG定义DEBUG宏，可能文件中有DEBUG宏部分的相关信息，用个DDEBUG来选择开启或关闭DEBUG
```

示例代码

```bash
// -Dname 定义宏name，默认定义内容为字符串"1"

#include <stdio.h>

int main() {
	#ifdef DEBUG
		printf("DEBUG LOG\n");
	#endif
		printf("in\n");
}
```

## 实例操作

### 直接在命令行中输入参数

```bash
g++ main.cpp src/swap.cpp -Iinclude -o wangyang
g++ main.cpp src/swap.cpp -Iinclude -Wall -std=c++11 -o wangyang
```

### 生成库文件并编译
链接**静态库**生产可执行文件
```bash
$cd src
$g++ swap.cpp -c -I../include
$ar rs libswap.a swap.o
$cd ..
$g++ main.cpp -Lsrc -lswap -Iinclude -o wangyang
```

链接**动态库**生成可执行文件
```bash
$cd src
$g++ swap.cpp -I../include -fPIC -shared -o libswap.so
# position independence code
$cd ..
$g++ main.cpp -Iinclude -Lsrc -lswap -o wangyang
```
这里生成的`wangyang`执行时会报错，链接不到动态库。两种办法解决
```bash
$export LD_LIBRARY_PATH=src/:$LD_LIBRARY_PATH
# 或者
$LD_LIBRARY_PATH=src ./wangyang

```

## `GDB`调试器

### 常用命令参数

* `GDB`(GNU Debugger)是一个用来调试`C/C++`程序的功能强大的调试器，是`Linux`系统开发`C/C++`最常用的调试器
* 学会用调试器，可以减少工作量

**GDB主要功能**

* 设置断点
* 观察制定位置的程序执行
* 单步执行程序，便于调试
* 查看变量值的变化
* 动态改变程序的执行环境
* 分析崩溃程序产生的执行文件

### 常用调试命令参数

调试执行，执行`GDB [exefilename]`，进入gdb调试程序
```bash
$(gdb)help(h)

$(gdb)run(r)

$(gdb)start

$(gdb)list(l)

$(gdb)set

$(gdb)next(n) # 单步调试，逐过程

$(gdb)step(s) # 单步调试，逐语句

$(gdb)backtrace(bt)

$(gdb)frame(f)

$(gdb)info(i)

$(gdb)finish

$(gdb)continue(c)

$(gdb)print(p)

$(gdb)quit(q)
```

```bash
$(gdb)break+num(b)

$(gdb)info breakpoints

$(gdb)delte breakpoints num(d)

$(gdb)display

$(gdb)undisplay

$(gdb)watch

$(gdb)i watch

$(gdb)enable breakpoints

$(gdb)disable breakpoints

$(gdb)x

$(gdb)run argv[1] argv[2]

$(gdb)set follow-fork-mode child

```

> Tips:
> 1. 编译程序必须添加`-g`选项，不然没用
> 2. 回车键，重复上一条命令

### 命令行调试

```C++
#include <iostream>
using namespace std;

int main(int argc, char *argv[]) {
  int N = 100;
  int sum = 0;
  int i = 1;

  // calculate sum from 1 to 100
  while (i <= N) {
    sum = sum + i;
    i = i + 1;
  }

  cout << "sum = " << sum << endl;
  cout << "The program is over." << endl;

  return 0;
}
```

### 快捷键
![vscode](./vscode.png)


|功能|快捷键|功能|快捷键|
|:--:|:--:|:--:|:--:|
|跳转文件|Ctrl+p|关闭当前文件|Ctrl+w|
|打开命令面板|Ctrl+Shift+p|关闭当前文件|Alt+up/down|
|打开终端|Ctrl+`|变量统一命名|F2|
|关闭侧边栏|Ctrl+B|转到定义处|F12|
|复制文件|Ctrl+C|粘贴文本|Ctrl+v|
|保存文件|Ctrl+S|撤销操作|Ctrl+z|

**在Ctrl+p**窗口下还可以:
* 直接输入文件名，跳转到文件
* `?`列出当前可执行的动作
* `!`显示`Errors`或`Warnings`，也可以`Ctrl+Shift+M`
* `:`跳转到行数，也可以`Ctrl+G`直接进入
* `@`跳转到`symbol`(搜索变量或者函数)，也可以`Ctrl+Shift+O`直接进入
* `@`根据分类跳转`symbol`，查找属性或函数，也可以`Ctrl+Shift+O`后输入`:`进入
* `#`根据名字查找`symbol`，也可以`Ctrl+T`

**快捷键：编辑器与管理窗口**
1. 打开一个新窗口，`Ctrl+Shift+N`，这个我这里可以用
2. 关闭一个窗口，`Ctrl+Shift+W`，这里好像被`飞书`占用了。
3. 同时打开多个编辑器(查看多个文件)
4. 新建文件`Ctrl+N`
5. 文件之间切换`Ctrl+Tab`
6. 切出一个新的编辑器(最多3个)`Ctrl+\`
7. 左中右`3`个编辑器的快捷键`Ctrl+1`, `Ctrl+2`, `Ctrl+3`
8. `3`个编辑器之间循环切换`ctrl+
9. 编辑器换位置，`Ctrl+k`然后按`Left`或`Right`

**代码编辑相关的快捷键**

**快捷键: 格式调整**
1. 代码缩进`Ctlr+[`, `Ctrl+]`
2. `Ctrl+C`, `Ctrl+V`复制或者剪切当前行
3. 代码格式化`Shift+Alt+F`或`Ctrl+Shift+I`
4. 上下移动一行: `Alt+up`或`Alt+Down`
5. 向上或向下复制一行: `Shift+Alt+up`或`Shift+Alt+Down`
6. 在当前行下边输入一行: `Ctrl+enter`
7. 在当前行上边输入一行: `Ctrl+Alt+enter`

**快捷键：光标相关**
1. 移动行收：home
2. 移动行尾: End
3. 移动到文件结尾: `Ctrl+end`
4. 移动到文件开口: `Ctrl+home`
5. 移动到定义处: `F12`
6. 定义处缩略图：只看一眼不跳转过去`Alt+F12`
7. 移动到后半个括号:`Ctrl+Shift+]`
8. 光标到行尾: `Shift+end`
9. 光标到行手: `Shift+home`
11. 扩展/缩小选取范围: `Shift+Alt+Left`和`Shift+Alt+Right`
10. 删除光标右侧所有字: `Ctrl+Delete`
12. 多行编辑(列编辑): `Alt+Shift+left click`和`ctrl+Alt+down/up`
13. 同时选中所有匹配：`Ctrl+Shift+L`
14. `Ctlr+D`下一个匹配也被选中
15. 回退上一个光标操作`Ctrl+U`

**快捷键: 代码重构**
1. 找到所有的引用：`Shift+F12`
2. 同时修改文件中所有匹配的:`Ctrl+F12`
3. 重命名: 比如要修改一个方法名，可以选中后按`F2`，输入新的名字，回车，会发现所有的文件都修改了
4. 跳转到下一个`Error`或`warning`:当有多个错误时可以按`F8`逐个跳转
5. 查看`diff`，在`explorer`里选择文件右键`set file to compare`，然后需要对比文件上右键选择`compare with file_name_you_chose`

**快捷键: 查找替换**
1. 查找`Ctrl+F`
2. 查找替换`Ctrl+H`
3. 整个文件夹中查找`Ctrl+Shift+H`
 
**快捷键: 显示相关**
1. 全屏：`F11`
2. zoom in/out: `Ctrl + +/-`
3. 显示资源管理器:`Ctrl+Shift+E`
4. 显示搜索:`Ctrl+Shift+F`
5. 显示Git: `Ctrl+Shift+G`
6. 显示Debug: `Ctrl+Shift+D`
7. 显示Output: `Ctrl+Shift+U`

# CMake
**前言**:
* **CMake**是一个跨平台的安装编译工具，可以用**简单**的语句来描述所有平台的的安装(编译过程)
* CMake已经是大部分**C++开源项目的标配**

## Cross-platform development

**没有CMAKE**
* 不同平台没有办法统一，都是各自独立，`win,macos,linux`各自做各自的。

**有CMake**
* 可以通过`CMakeLists.txt`然后使用`cmake`工具来管理

### 语法特性
* **基本语法格式**：指令(参数1 参数2 ...)
* * 参数使用**括弧**括起
* * 参数之间使用空格或分号分开
* **指令**是大小写无关的，**参数**是大小写相关的
```cmake
set(HELLO hello.cpp)
add_executable(hello main.cpp hello.cpp)
ADD_EXECUTABLE(hello main.cpp ${HELLO})
IF(HELLO)
if(${HELLO}) error
```
* 变量使用`${}`方式取值，但是在**IF**控制语句中是直接使用变量名

## 重要指令和`CMAKE`常用变量

### 重要指令

* cmake_minimum_required - **指定`CMAKE`的最小版本要求**
  * 语法: cmake_minimum_required(VERSION versionNumber [FATAL_ERROR])
  ```cmake
  # CMake最小版本要求2.8.3
  cmake_minimum_required(VERSION 2.8.3)
  ```

* project - **定义工程名称，并可制定工程支持语言**
  * 语法: project(projectname [CXX][C][Java])
  ```cmake
  # 指定工程名为HELLOWORLD
  project(HELLOWORLD)
  ```

* set - **显示的定义变量**
  * 语法: set(VAR [VALUE][CACHE TYPE DOCSTRING [FORCE]]])
  ```cmake
  # 定义SRC变量，其值为syahello.cpp hello.cpp
  set(SRC sayhello.cpp hello.cpp)
  ```

* include_directories - **向工程添加多个特定的头文件搜索路径--->相当与指定`g++`编译器的-I参数**
  * 语法: include_directories([AFTER|BEFORE][SYSTEM] dir1 dir2 ...)
  ```cmake
  # 将`/usr/include/myincludefolder 和 ./include 添加到头文件搜索路径
  include_direcotories(/usr/include/myincludefolder ./include)
  ```

* link_libraries - **向工程添加多个特定库文件的搜索路径--->相当与指定`g++`编译器的-L参数**
  * 语法: link_libraries(dir1 dir2)
  ```cmake
  # 将/usr/lib/mylibfolder 和 ./lib 添加到库文件的搜索路径
  link_libraries(/usr/lib/mylibfolder ./lib)
  ```

* add_library - **生成库文件**
  * 语法: add_library(libname [SHARED|STATIC|MODULE][EXCLUDE_FROM_ALL] source1 source2 ...)
  ```cmake
  # 通过变量SRC生成libhello.so共享库
  add_library(hello SHARED ${SRC})
  ```

* add_compile_options - **添加编译参数**
  * 语法: add_compile_options(<options> ...)
  ```cmake
  # 添加编译参数 -Wall -std=c++11
  add_compile_options(-Wall -std=c++11 -O2)
  ```

* add_executable - **生成可执行文件**
  * 语法: add_executable(exename source1 source2 ... sourceN)
  ```cmake
  # 编译main.cpp生成可执行的main
  add_executable(main main.cpp)
  ```

* target_link_libraries - **为target添加需要链接的共享库 --> 相同于指定`g++`编译器的`-l`参数**
  * 语法: target_link_libraries(target library1<debug | optimized> library2 ...)
  ```cmake
  # 将hello动态库文件链接到可执行文件main
  target_link_libraries(main hello)
  ```

* add_subdirectory - **向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制存放的位置**
  * add_subdirecoty(source_dir [binary_dir][EXCLUDE_FROM_ALL])
  ```cmake
  # 添加src子目录，src中需要有一个CMakeLists.txt
  add_subdirectory(src)
  ```

* aux_source_directory - **发现一个目录下所有的源文件并将列表存储在一个变量中，这个指令临时被用来自动构建源文件列表**
  * 语法: aux_source_diretory(dir VARIBLE)
  ```cmake
  # 定义SRC变量，其值为当前目录下所有的源代码文件
  aux_source_directory(. SRC)
  # 编译SRC变量所代表的源文件，生成main可执行文件
  add_executable(main ${SRC})

### CMake常用变量
* **CMAKE_C_FLAGS** gcc编译选项
* **CMAKE_CXX_FLAGS** g++编译选项
```cmake
# 在CMAKE_CXX_FLAGS编译选项后追加-std=c++11
set( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11" )
```
* **CMAKE_BUILD_TYPE** 编译类型(Debug, Release)
```cmake
# 设定编译类型为debug，调试时需要选择debug
set(CMAKE_BUILD_TYPE Debug)
# 设定编译类型为release，调试时需要选择release
set(CMAKE_BUILD_TYPE Release)
```
* **CMAKE_BINARY_DIR**
  PROJECT_BINARY_DIR
  **<projectname>_BINARY_DIR**
    1. 这三个变量指代的内容是一致的
    2. 如果是`in source build`, 指得就是工程顶层目录
    3. 如果是`out-of-source`编译，指得就是工程编译发生的目录
    4. PROJECT_BINARY_DIR 跟其他指令稍有区别，不过现在可以理解为一致

* **CMAKE_SOURCE_DIR**
  PROJECT_SOURCE_DIR
  **<projectname>_SOURCE_DIR**
    1. 这三个变量指代的内容是一致的，不论采用何种编译方式，都是工程顶层目录
    2. 也就是`in source build`时，他跟`CMAKE_BINARY_DIR`等变量一致
    3. `PROJECT_SOURCE_DIR` 跟其他指令稍有区别，现在也可以理解为一致

* **CMAKE_C_COMPILER**: 指定C编译器
* **CMAKE_CXX_COMPILER**: 指定C++编译器
* **EXECUTABLE_OUTPUT_PATH**: 可执行文件输出的存放路径
* **LIBRARY_OUTPUT_PATH**: 库文件输出的存放路径

## CMake编译工程
CMake目录结构: 项目主目录存在一个`CMakeLists.txt`文件

**两种方式设置编译规则**
1. 包含源文件的子文件夹**包含**CMakeLists.txt文件，主目录的CMakelists.txt通过add_subdirectory添加子目录即可;
2. 包含源文件的子文件夹**未包含**CMakeLists.txt文件，子目录编译规则体现在CMakeLists.txt中

### 编译流程

**在linux平台下使用CMake构建C/C++工程的流程如下**
* 手动编写CMakeLists.txt
* 执行命令`cmake PATH`生成`Makefile`（PATH是顶层CMakeLists.txt所在目录)
* 执行命令`make`

```cmake
# importants tips
.   # 表示当前目录
./  # 表示当前目录

..  # 表示上级目录
../ # 表示上级目录
```

### 两种构建方式

* 内部构建`in source build`: 不推荐使用
  
  内部构建会在同级目录下产生一堆中间文件，这些中间文件并不是我们最终所需要的，将其和工程文件放在一起会显得杂乱无章
  ```cmake
  ## 内部构建

  # 在当前目录下，编译本目录的CMakeLists.txt，生成Makefile和其他文件
  cmake .
  # 执行make命令，生产target
  make
  ```
* 外部构建`out of source build`: 推荐使用

  将编译输出文件与源文件放到不同目录中
  ```cmake
  ## 外部构建

  # 1. 在当前目录下，创建build文件夹
  mkdir build
  # 2. 进入到build文件夹
  cd build
  # 3. 编译上级目录的CMakeLists.txt，生成Makefile和其他文件
  cmake ..
  # 4. 执行make命令，生成target
  make
  ```