

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

6. 发送

    ```c++
    err = send(sock_i, pkt_buf, pktlen, 0);
    ```

    