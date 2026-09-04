# Task 4：思考题

## 1. Make 和简单的 build.sh 有什么区别？
build.sh 只是简单顺序执行所有编译命令，每次都会重新编译所有文件。而 Make 
会根据依赖关系和时间戳进行增量构建，只编译必要的部分


## 2. CMake 是编译器吗？

执行 `cmake --build build` 时，最终是谁在编译 C 源文件？


不是。CMake 是一个构建系统生成器，它根据 CMakeLists.txt 生成特定平台的构
建文件（如 Makefile 或 Ninja 文件）。真正编译源代码的是底层的编译器（
如 gcc）。执行 cmake --build build 时，它会调用相应的构建工具（
如 make），再由 make 调用 gcc 进行编译


## 3. 为什么不希望每次都重新编译所有 `.c` 文件？
因为编译非常耗时，尤其在大型项目中。只重新编译修改过的文件和受影响的文件可
以节省大量时间，加快开发迭代速度
# 以下为截图

<img width="676" height="241" alt="image" src="https://github.com/user-attachments/assets/51317922-56a8-4211-b2fc-da458ddb4c6f" />

# 以下为一些笔记
## 一、Makefile和CMake的必要性
在项目中，最简单则直接使用编译器
```
g++ main.cpp -o main
```
但多时每次手动输入则麻烦，且如果仅修改部分，则不必要重新编译所有文件
则构建工具管理：
	源代码
	编译过程
	文件之间的依赖关系
	增量编译
	链接
	不同平台的构建

其中常见的工具就是：
```
Make / Makefile
CMake
```
## 二、 C++基本编译过程
假设有简单项目
```
main.cpp
math.cpp
```
理解为
```
main.cpp ──编译──> main.o
math.cpp ──编译──> math.o

main.o + math.o
       │
       │ 链接
       ↓
     Hello
```
即
```
源文件
  ↓
编译
  ↓
目标文件 .o
  ↓
链接
  ↓
可执行文件
```
## 三、 Make 和 Makefile
### 1. Makefile
	描述项目如何构建的规则文件
如
```
main: main.o student.o
	g++ main.o student.o -o main

main.o: main.cpp
	g++ -c main.cpp

student.o: student.cpp student.h
	g++ -c student.cpp
```
理解为
```
main
 ↓
依赖 main.o 和 student.o

main.o
 ↓
依赖 main.cpp

student.o
 ↓
依赖 student.cpp 和 student.h
```
### 2. 语法Makefile
核心：
```
目标: 依赖
	命令
```
注意：
命令前面通常必须使用 Tab，而不是空格。
## 四、 Make增量编译
核心思想：
	根据依赖关系，只重新构建发生变化的部分。
## 五、 Makefile中变量
Makefile 可以定义变量：
```
CXX = g++
CXXFLAGS = -Wall -std=c++17
```
使用：
```
$(CXX)
$(CXXFLAGS)
```
常见编译参数：
```
-Wall       开启较多警告
-g          生成调试信息
-O2         优化
-std=c++17  使用 C++17
```
## 六、 CMake
#### Make
> 按照 Makefile 中的规则执行构建

#### Makefile
> 告诉 Make 应该如何构建。
#### CMake
> 根据 `CMakeLists.txt` 生成构建系统
即
```
CMakeLists.txt
       ↓
     CMake
       ↓
  Makefile / Ninja
       ↓
      Make
       ↓
      g++
       ↓
    可执行文件
```
CMake 本身更加像一个构建系统生成器
## 七、 CMakeLists.txt
CMake 项目中核心的文件
如：
```
cmake_minimum_required(VERSION 3.15)

project(HelloCMake)

set(CMAKE_CXX_STANDARD 17)

add_executable(Hello
    main.cpp
    math.cpp
)
```
## 八、 CMake命令
1. cmake_minimum_required()
如表示指定项目所要求的最低 CMake 版本：
```
cmake_minimum_required(VERSION 3.15)
```
2. project()
如表示定义项目名称
```
project(HelloCMake)
```
3. set()
如设置 C++ 标准为 C++17：
```
set(CMAKE_CXX_STANDARD 17)
```
4. add_executable()
如创建一个名为 `Hello` 的可执行程序，它由 `main.cpp` 和 `math.cpp` 构建：
```
add_executable(Hello
    main.cpp
    math.cpp
)
```
此处target即为Hello

CMake 的很多操作，本质上是：

> **给 Target 添加源文件、头文件、编译选项、链接库等属性。**
## 九、 CMake 的基本工作流程
```
mkdir build
cd build
cmake ..
cmake --build .
```
1. 配置生成
```
cmake ..
```
CMake：
```
读取 CMakeLists.txt
        ↓
检查编译器
        ↓
分析项目
        ↓
分析依赖
        ↓
生成构建系统
```
即生成Makefile等
2. 真正构建
```
cmake --build .
```
即：
```
Make
 ↓
g++
 ↓
编译 .cpp
 ↓
生成 .o
 ↓
链接
 ↓
可执行文件
```
## 十、 遇错
1. 
```
The C compiler identification is GNU 15.2.0
The CXX compiler identification is unknown
```
重配即可
2. 
```
 Cannot find source file:

src/main.cpp
```
项目结构修改即可
## 十一、 串联

```
                    CMakeLists.txt
                          │
                          ↓
                        CMake
                          │
                    cmake ..
                          │
                          ↓
                  检查 C/C++ 编译器
                          │
                          ↓
                   生成构建系统
                          │
                    Makefile
                          │
                          ↓
                 cmake --build .
                          │
                          ↓
                        Make
                          │
                          ↓
                        g++
                          │
             ┌────────────┴────────────┐
             ↓                         ↓
         main.cpp                  math.cpp
             ↓                         ↓
          main.o                    math.o
             └────────────┬────────────┘
                          ↓
                         链接
                          ↓
                        Hello
```

| 工具             | 作用                       |
| -------------- | ------------------------ |
| g++            | 编译、链接 C++                |
| gcc            | 编译、链接 C                  |
| make           | 根据 Makefile 执行构建         |
| Makefile       | 描述 Make 如何构建项目           |
| cmake          | 根据 CMakeLists.txt 生成构建系统 |
| CMakeLists.txt | 描述 CMake 项目如何构建          |


