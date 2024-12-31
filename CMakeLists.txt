	#1.cmake verson，指定cmake版本
	cmake_minimum_required(VERSION 3.2)
	
	#2.project name，指定项目的名称，一般和项目的文件夹名称对应,声明一个cmake工程，工程名为cmakelist_test
	PROJECT(cmakelist_test VERSION 0.1.0)
	
	#3.添加c++ 11标准支持，如果程序中使用了C++11标准，则需要设置告诉编译器，没有可以不用写。
	set( CMAKE_CXX_FLAGS "-std=c++11")
	
	#4.设置编译器编译模式，对于编译用的Debug模式和调试用的Release模式，在Debug模式中，程序运行较慢，当可以在IDE中进行断点调试，而Release模式则速度较快，但没有调试信息。默认是Debug模式。
	set( CMAKE_BUILD_TYPE "Debug")
	
	#5.添加引用的第三方头文件，此项目main.cpp引用的头文件有Sum.h，因此需要添加头文件目录，因为和CMakeList.txt同级，所以此处目录只写include即可，应写对头文件所在的目录，以防找不到相应的头文件而报错，如果难以确定就直接写上绝对路径即可
	include_directories(include)    
	
	
	# ----src下面有多个.cpp需要编译,相应的库函数也都有,这个时候可以执行下面的步骤-------
	# #6.source directory，源文件目录,将源文件目录/路径 src赋值给DIR_SRCS
	# AUX_SOURCE_DIRECTORY(src DIR_SRCS)
	# #7.set environment variable，设置环境变量，编译用到的源文件全部都要放到这里，否则编译能够通过，但是执行的时候会出现各种问题，比如"symbol lookup error xxxxx , undefined symbol"
	# SET(TEST_MATH ${DIR_SRCS}) #将编译用的源文件所在路径DIR_SRCS赋值给TEST_MATH
	# # #8.编译生成库文件，将Sum.cpp生成共享库文件libSum.so，这条命令告诉cmake，把这些源文件编译成一个叫作“Sum”的共享库,其实有了6和7没有必要再执行这一步
	# # add_library(Sum SHARED src/xxx1.cpp src/xxx2.cpp ...)
	# #9.add executable file，添加要编译的可执行文件
	# ADD_EXECUTABLE(${PROJECT_NAME} ${TEST_MATH}) #这里生成的可执行文件的名字为项目名字，${PROJECT_NAME}就是#2中PROJECT(cmakelist_test)的项目名字cmakelist_test
	# #10.add link library，添加可执行文件所需要的库，比如我们用到了libm.so（命名规则：lib+name+.so），以及生成的libxxx.so,就添加这些库的名称
	# TARGET_LINK_LIBRARIES(${PROJECT_NAME} m)
	
	
	#6.编译生成库文件，将Sum.cpp生成共享库文件libSum.so，这条命令告诉cmake，把这些源文件编译成一个叫作“Sum”的共享库
	add_library(Sum SHARED src/Sum.cpp)
	
	#使用add_library(Sum Sum.cpp)能同时生成静态库文件libSum.a和动态库文件libSum.so
	#使用add_library(Sum STATIC Sum.cpp)能生成静态库文件libSum.a
	#在linux中，库文件分为静态库和动态库两种，静态库以.a作为后缀名，共享库以.so结尾。所有库都是一些函数打包后的集合，差别在于静态库每次被调用都会生成一个副本，而共享库则只有一个副本，更省空间。
	#同时add_library(Sum SHARED Sum.cpp)后面的源文件可以接任意多个，同时生成一个库文件，例如add_library(Sum SHARED Sum.cpp b.cpp d.cpp hello.a ...)
 
	
	#7.add executable file，添加要编译的可执行文件，编译main.cpp，生成可执行文件main，也可以将main写成${PROJECT_NAME}，即为当前项目名称，就是#2中PROJECT(cmakelist_test)的项目名字cmakelist_test
	add_executable(main src/main.cpp)   
	
	#8.add link library，添加可执行文件所需要的库，比如我们用到了libSum.so（命名规则：lib+name+.so），就添加该库的名称
	target_link_libraries(main Sum)  #生成的主文件可执行文件main链接到共享库文件库libSum.so，可执行程序即可调用库文件中的函数