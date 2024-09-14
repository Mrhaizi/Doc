# 一堆.cpp文件怎么生成一个可执行文件

1. 预处理
    编译器首先会对.cpp文件进行预处理，项目中前面**带有#的都是预处理语句**，在此阶段都会进行处理,并且会删除注释，预处理后的文件 **扩展名为.i**
2. 编译
    将预处理文件转换为汇编语言文件，**扩展名为.s**
3. 汇编
    将汇编语言转换为机器码(二进制代码), 并生成目标文件,目标文件的扩展名为**.o或者.obj**
4. 链接
    将所有的目标文件(.o/.obj)和相关的库文件(静态库.a/.lib,动态库.so/.dll)链接起来
5. 生成可执行文件
    链接完后，在linux系统会生成一个**.out扩展名的文件，在windows上是.exe**
```shell
g++ -E main.cpp -o main.i
g++ -S main.i io main.s
g++ -c main.s -o main.o
g++ main.o -o my_program
```

# CMake基本用法

## 构建指令

### 旧版
```shell
cd /workspace
mkdir build && cd build
cmake .. && make -jx
```
### 新版(跨平台，在windows和linux以及mac都可以使用下面指令)
```shell 
cd /workspace
cmake -B build
cmake --build build -jx
```
## 基础语句介绍
### cmake_minimum_required(VERSION x.xx)
是CMake构建系统中的一个常用命令，用于指定 CMakeLists.txt 文件所要求的最低 CMake 版本。其主要目的是确保使用该构建脚本的开发环境满足最低 CMake 版本要求，以便避免因为版本不兼容导致的错误。

### project(<project_name> [LANGUAGES <language_name>...])
<project_name>：项目的名称，通常是一个简单的标识符。
LANGUAGES <language_name>：可选参数，指定项目使用的编程语言，例如 C、CXX（表示 C++)等。如果不指定语言，CMake 会默认支持 C 和 C++。
这个命令会生成几个较为常见的变量PROJECT_NAME PROJECT_SOURCE_DIR PROJECT_BINARY_DIR

### set(<variable> <value>... [CACHE <type> <docstring> [FORCE]])
用于设置变量的值,可以定义变量，修改变量值，可以将值设为空
<variable>：要设置的变量名。
<value>：赋给变量的值。可以有多个值，并且这些值将被连接成一个列表。
CACHE：如果指定此选项，变量会被存储在 CMake 的缓存中(CMakeCache.txt)。
FORCE：在使用 CACHE 选项时，FORCE 可以强制重写已存在的缓存变量。
<type>：指定缓存变量的类型，比如 STRING、BOOL、PATH 等。
<docstring>：为缓存变量提供注释说明，方便后续开发者理解这个变量的用途。

### file()
用于递归匹配文件
file(GLOB_RECURSE) 是递归地搜索目录及其子目录，匹配文件。
file(GLOB_RECURSE SRC "src/*.cpp")

### add_executable(<name> <source>)
<name>: 库的名字。
<source>: 源文件列表，即生成库所需要的源文件路径

### add_library(<name> [STATIC | SHARED | OBJECT] [EXCLUDE_FROM_ALL] <source>...)
<name>: 库的名字。
STATIC: 生成一个静态库 (.a)。静态库在编译时链接到可执行文件中，运行时不需要库文件。
SHARED: 生成一个共享库 (.so 或 .dll)，它在运行时动态加载。
OBJECT: 生成一个对象库（.o 或 .obj），可以用于链接其他目标
<source>: 源文件列表，即生成库所需要的源文件路径

### find_package(<package> [version] [EXACT] [REQUIRED] [COMPONENTS components...]   [MODULE] [CONFIG])
<package>: 要查找的库或包的名称，例如Eigen3、Opencv 等等。
version: 可选的参数，用于指定所需的包版本。
EXACT: 指定必须找到确切的版本号，否则无法接受。例如，指定 1.65.1 版本，CMake 只接受这个版本而不是其他版本。
REQUIRED: 如果指定这个关键字，找不到包时，CMake 会报错并停止配置过程。如果不指定 REQUIRED，则找不到包时不会报错，继续执行
COMPONENTS: 如果包由多个组件组成，使用 COMPONENTS 关键字可以指定你想使用的组件，比如Qt有几十个组件，但是你可以选择你自己仅需的 Gui Core 组件
MODULE: 告诉 CMake 查找模块模式的包查找文件（通常是 <package>Config.cmake 或 Find<package>.cmake）。
CONFIG: 仅查找配置模式的包配置文件（例如 packageConfig.cmake）。

### target_include_directories(<name> [PUBLIC | PRIVATE | INTERFACE] <include_dir>)
<name>: 库的名字。
<include_dir>: 头文件目录
PRIVATE: 头文件目录仅在编译当前目标时有效。其他与该目标链接的目标无法访问这些目录。
PUBLIC: 头文件目录不仅在编译当前目标时有效，其他与该目标链接的目标也会继承这些头文件目录。
INTERFACE: 头文件目录不会用于当前目标的编译，但其他与该目标链接的目标可以使用这些目录。这通常用于库的接口。

### target_link_libraries(<target_name> [PUBLIC | PRIVATE | INTERFACE] <library>)
<target_name>: 目标名称。可以是一个通过 add_library() 或 add_executable() 创建的目标，表示你想链接库的目标对象。
<library>: 要链接的库，可以是静态库（.a 文件）、动态库（.so 或 .dll 文件。
PRIVATE: 链接库只影响当前目标的编译过程，其他链接到此目标的目标不会受到影响。
PUBLIC: 链接库不仅用于当前目标，其他链接到此目标的目标也会继承这个库。
INTERFACE: 当前目标不会链接此库，但其他链接到此目标的目标会继承这个库。

### add_subdirectory(<source_dir> [binary_dir] [EXCLUDE_FROM_ALL])
<source_dir>: 子目录的路径，相对于当前 CMakeLists.txt 文件的路径，或者绝对路径。这个目录必须包含一个 CMakeLists.txt 文件。
binary_dir: 可选参数，指定该子目录的二进制文件（例如编译后的对象文件、库、可执行文件）应当存储在哪个目录中。如果不指定，CMake 会使用默认的构建目录。
EXCLUDE_FROM_ALL: 可选参数，如果指定了这个选项，子目录中定义的目标不会默认添加到 ALL 构建目标中。这意味着这些子目录不会参与主项目的默认构建过程，除非手动构建这些目标(make xxxx)。
## 一个标准模版
```CMake
cmake_minimum_required(VERSION 3.17)
project(demo LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

file(GLOB_RECURSE SRC src/*.cpp) 

add_executable(${PROJECT_NAME} ${SRC})
```
# c++基础内容
## 数据类型
char、short、int、long、long long：这些类型表示整数，大小通常为 1、2、4、4 或 8 字节
float、double、long double：用于表示带小数的数字，大小通常为 4、8、16 字节。
bool：表示真或假，底层通常使用 1 个字节存储，但实际上只需要 1 位。
