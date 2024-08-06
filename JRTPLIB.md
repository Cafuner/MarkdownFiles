实例教程：https://blog.csdn.net/zhizhengguan/article/details/124115464

按照这个教程，配好环境后，编译还是会出错，需要把报错的地方进行如下修改：

将所有jrtplib中src下的头文件中包含jmutex.h和jthread.h的include全部修改为include "jmutex.h"和include"jthread.h"。例如，原来是#include "jthread/jmutex.h"，那么就修改为#include "jmutex.h"。