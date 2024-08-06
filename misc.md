文本文件和二进制文件的区别：

文本文件是指文件的内容是由字符组成的，而这些字符所采取的编码方式可能是ASCII，可能是UTF-8、UTF-16等。因此，读取文本文件时，需要明确字符的编码方式，然后对照编码表将字符逐一翻译。

二进制文件则是指每个字节的含义都是由文件创建者自己决定的。比如，一个可执行文件就是一个二进制文件。我们显然不能把二进制文件按照字符对照表翻译，因为其中有很多指令、地址等。





在命令行运行java命令时出现异常:

Error occurred during initialization of VM
java.lang.Error: Properties init: Could not determine current working directory.
at java.lang.System.initProperties(Native Method)
at java.lang.System.initializeSystemClass(System.java:1166)

很可能是因为当前目录被删除过。比如我们在idea中用maven命令clean了target目录，然后有使用package命令生成了该目录，那么我们的命令行窗口需要先退出到上一层目录，再重新进入target目录，否则会出现上面的异常。