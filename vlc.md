tplink管理页面 tplogin.cn 密码network123

树莓派用户名 pi，密码network123

可在tplink界面查看pi的ip地址，从而使用ssh远程登录





发送Intereset

1. 构造目标name和name的长度

    ```c++
    char *name;
    size_t nlen;
    ```

2. 构造interest_pkt

    ```c++
    struct interest_pkt *ipkt = malloc(sizeof(*ipkt));
    ipkt->name = NULL;
    ipkt->nonce = 8301;
    ipkt->life_time = 4000;
    ipkt->hop_limit = 128;
    ipkt->flags = I_CANBEPREFIX;
    ```

3. 创建 raw socket

    ```c++
    int sock_i = socket(AF_NNET, SOCK_RAW, 1);
    ```

4. 创建组包缓存

    ```c++
    unsigned char* pkt_buf = malloc(I_MAX_LEN);
    ```

5. 组包

    ```c++
        pktlen = encap_interest_buf(fname, fnlen, ipkt->life_time, 
                ipkt->hop_limit, ipkt->nonce, 
                ipkt->flags, pkt_buf, I_MAX_LEN);
    ```


6. 发包

    ```c++
    err = send(sock_i, pkt_buf, pktlen, 0);
    ```





接受interest

可以通过以下命令查看路由表：

```shell
cat /proc/nip/fib_proc1
···
/Rpi-1/lab1038,32770,enx000ec6499237,0000ffffffff
```





VLC

1.关于如何从vlc界面输入目标视频的名字，而不是ip地址和端口号，仍然可以加载rtp模块

在vlc界面，左上角菜单栏选择Media->Open Network Stream。输入url，格式为:" rtp://`name`"。

在modules/access/rtp/rtp.c文件中Open函数，通过demux->psz_location可以获得name。



2.modules/access/rtp/input.c文件中rtp_dgram_thread函数中对输入的准备工作

声明一个msghdr类型结构体`msg`，在其中放入一个iovec结构体`iov`。使用block_Alloc函数分配一块空间，并让iov的`iov_base`指向这个block中buffer的起始地址。调用recvmsg函数，将接收到的内容放入msg。



3.关于如何替换接受函数

vlc源代码使用recvmsg函数，将收到的数据放入了`msg.iov[0].iov_base`，该空间大小为 `msg.iov[0].iov_len` 。目前recvmsg和recv的详细区别还不是很清楚。尝试直接使用recv函数把数据放入上面的地址，是可以跑通的。因此先尝试使用ICN的socket接受数据，将RTP包放入上述地址。



4.视频ts文件如何命名，客户端请求一个视频，应该如何分段呢？

目前简单地将视频文件按每x个ts包分为一组，一个RTP包传输一组。每个RTP包携带的ts包的个数为 （MTU - RTP header size - nip header size）。





在树莓派上用vlc的命令行指令推流

```
vlc Test.ts --sout "#rtp{mux=ts{pid-video=100,pid-audio=101,pid-pmt=98},dst=192.168.3.221,port=1234}" --sout-all --sout-keep --loop
```



BUG：

如果客户端无法接收到返回的Data，打印一下客户端的转发表，如果不能正常打印，可能是哪里有bug，重启一下客户端。













