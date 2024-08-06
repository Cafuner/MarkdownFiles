## NIP

NIP is a data-centric network protocol stack. It is a kernel-level implementation of ICN \(Information-Centric Networking) designed to meet the future development needs of the Internet. It will support massive connections, improve communication efficiency, and reduce the difficulty of network application development.

Similar to the TCP/IP network protocol stack, NIP works in the Kernel, providing a set of operation primitives to the upper layer. When developing network applications with NIP, there is no need to care about the underlying architecture and mechanics, or even the address of a host. Developers only need to focus on data itself, which makes development easier and faster.

##### <!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

##### <!-- code_chunk_output -->

- [NIP](#nip)
  - [Installing NIP](#installing-nip)
    - [First Choice: Using OS image](#first-choice-using-os-image)
      - [Ubuntu](#ubuntu)
      - [Raspberry Pi](#raspberry-pi)
    - [Second Choice: Compile Kernel and Install Module](#second-choice-compile-kernel-and-install-module)
      - [Step 1 : Download Kernel Source Code](#step-1-download-kernel-source-code)
      - [Step 2 : Replace Files](#step-2-replace-files)
      - [step 3 : compile and start with new kernel image](#step-3-compile-and-start-with-new-kernel-image)
      - [step 4 : insert NIP kernel module](#step-4-insert-nip-kernel-module)
  - [Getting Started](#getting-started)
    - [File Provider](#file-provider)
      - [C Program & Comment](#c-program-comment)
      - [socket() - API Definition](#socket-api-definition)
        - [Name](#name)
        - [Synopsis](#synopsis)
        - [Parameters:](#parameters)
        - [Return Value](#return-value)
        - [Example](#example)
        - [Errors](#errors)
      - [bind() - API Definition](#bind-api-definition)
        - [Name](#name-1)
        - [Synopsis](#synopsis-1)
        - [Parameters:](#parameters-1)
        - [Return Value](#return-value-1)
        - [Example](#example-1)
        - [Errors](#errors-1)
      - [publish() - API Definition](#publish-api-definition)
        - [Name](#name-2)
        - [Synopsis](#synopsis-2)
        - [Parameters:](#parameters-2)
        - [Return Value](#return-value-2)
        - [Example](#example-2)
        - [Errors](#errors-2)
      - [status() - API Defination](#status-api-defination)
        - [Name](#name-3)
        - [Synopsis](#synopsis-3)
        - [Parameters:](#parameters-3)
        - [Return Value](#return-value-3)
        - [Example](#example-3)
        - [Errors](#errors-3)
      - [isend() - API Defination](#isend-api-defination)
        - [Name](#name-4)
        - [Synopsis](#synopsis-4)
        - [Parameters:](#parameters-4)
        - [Return Value](#return-value-4)
        - [Example](#example-4)
        - [Errors](#errors-4)
    - [File Consumer](#file-consumer)
      - [C Program & Comment](#c-program-comment-1)
      - [touch() - API Definition](#touch-api-definition)
        - [Name](#name-5)
        - [Synopsis](#synopsis-5)
        - [Parameters:](#parameters-5)
        - [Return Value](#return-value-5)
        - [Example](#example-5)
        - [Errors](#errors-5)
      - [request() - API Definition](#request-api-definition)
        - [Name](#name-6)
        - [Synopsis](#synopsis-6)
        - [Parameters:](#parameters-6)
        - [Return Value](#return-value-6)
        - [Example](#example-6)
        - [Errors](#errors-6)
      - [irecv() - API Defination](#irecv-api-defination)
        - [Name](#name-7)
        - [Synopsis](#synopsis-7)
        - [Parameters:](#parameters-7)
        - [Return Value](#return-value-7)
        - [Example](#example-7)
        - [Errors](#errors-7)
    - [Chat Client](#chat-client)
      - [C Program with Comment](#c-program-with-comment)
      - [watch() - API Defination](#watch-api-defination)
        - [Name](#name-8)
        - [Synopsis](#synopsis-8)
        - [Parameters:](#parameters-8)
        - [Return Value](#return-value-8)
        - [Example](#example-8)
        - [Errors](#errors-8)
      - [cast() - API Defination](#cast-api-defination)
        - [Name](#name-9)
        - [Synopsis](#synopsis-9)
        - [Parameters:](#parameters-9)
        - [Return Value](#return-value-9)
        - [Example](#example-9)
        - [Errors](#errors-9)
  - [Resources](#resources)

<!-- /code_chunk_output -->


### Installing NIP

Currently NIP can be deployed on Ubuntu 18.04 \(Linux Kernel v4.14, platform: x86) and Raspberry \(Linux Kernel v4.19, platform: arm) Pi OS.  More platforms and kernel versions will be support in the future.

There are 2 choice to install NIP: 
- Use the operating system image \(.iso)
- Compile Kernel and insert Module \(nip.ko)

#### First Choice: Using OS image

##### Ubuntu

| OS image | kernel | platform | 
| - | - | - |
|[Ubuntu Desktop 18.04.1.iso](https://link)|v4.14|x86|

This is used for:

+ Setup NIP environment on x86 platform
+ For both VMs and physical machines


It contains:

+ Modified Linux kernel 4.14.
+ NIP header files in PATH
+ NIP kernel module (nip.ko), insert on boot.
+ Preset examples.

##### Raspberry Pi

| OS image | kernel | platform | 
| - | - | - |
|[Raspberry Pi OS.iso](https://link)|v4.19|arm|
This is used for:

+ Setup NIP environment on Raspberry Pi 4B+
+ (Untested for Rpi 3)

It contains:

+ Modified Linux kernel 4.19
+ NIP header files in PATH
+ NIP kernel module (nip.ko), insert on boot.
+ Preset examples.

#### Second Choice: Compile Kernel and Install Module

##### Step 1 : Download Kernel Source Code

```sh
wget https://mirrors.edge.kernel.org/pub/linux/kernel/v4.x/linux-4.14.195.tar.xz
# or download other versions, or from other repo
tar -Jxvf linux-4.14.195.tar.xz
```

##### Step 2 : Replace Files

There are serveral files should be changed for deploying NIP. Find the corresponding subfolder under `changeKernelFiles/`, then: 
```sh
cp -r changedKernelFiles/linux-4.14/* /usr/src/linux-4.14.195/
# or add 'sudo' if permission denied.
```

##### step 3 : compile and start with new kernel image

Locally compile or cross compile the changed source code. See more details [here](https://link_compiling_autosetup). Take Ubuntu for example:

```sh
# for Ubuntu
cd /usr/src/linux-4.14.195/ 
sudo make menuconfig 
sudo make -j4 
sudo make modules_install 
sudo make install
```

Then edit `/etc/default/grub`, so that System can start with the new kernel image.

##### step 4 : insert NIP kernel module

After starting with the new kernel image, NIP kernel module can be inserted by executing:

```sh
sudo insmod nip.ko
```

Then we can use NIP's APIs for development.

### Getting Started

Let's see some simple NIP demos. `producer.c` and `consumer.c` is an example of file transferring. The former is designed for the file server and the latter is for the file client. `chat_client.c` is an demo where Alice and Bob can chat with each other.

#### File Provider

##### C Program & Comment

```c
// for development with NIP, "isockethdr.h" should be included
#include <stdio.h>
#include <errno.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <linux/isockethdr.h>

int get_file_size(char *filename)
{
	struct stat statbuf;
	stat(filename, &statbuf);
	return statbuf.st_size;
}

int main()
{
        int sock, err, i;
	// set prefix and name for bind() and publish()
	char *prefix = "/Rpi-1/lab1038";
        char *name = "/Rpi-1/lab1038/info/about/10Mfile.txt";
        
	// read the file and copy it to buffer
	size_t nlen = strlen(name);
        char *file_name = "../../testFile/10Mfile.txt";
	int file_fp, flen;
	flen = get_file_size(file_name);
        size_t dlen = flen;
	char *data = malloc(flen);
	if (!data) {
		printf("[!] Data malloc failed \n");
		return 1;
	}
	file_fp = open(file_name,O_RDONLY,0777); 
	if (file_fp < 0) {
		printf("File:%s Not Found\n", file_name); 
		return 1;
        }
	flen = read(file_fp, data, dlen);
	close(file_fp);

	// construct the "status_info" for status()
	struct status_info stat_info;
	stat_info.nlen = 1024;
	stat_info.name_buf = malloc(stat_info.nlen);
	if (!stat_info.name_buf) {
		printf("[!] stat_info.name_buf malloc failed \n");
		return 1;
	}
	memset(stat_info.name_buf, 0, 1024);

	// create NDP socket
        sock = socket(AF_NNET, SOCK_NDP, htons(ETH_P_NIP));
        if (sock < 0) {
                printf("[!] Socket created failed\n");
                printf("[!] Emsg: %s\n", strerror(errno));
                return 1;
        }

	// bind socket with a nsockaddr, which includes a prefix
        struct nsockaddr addr;
        addr.family = AF_NNET;
        addr.enable_mask = BIND_PREFIX;
        addr.prefix = prefix;  
        addr.plen   = strlen(prefix);
        err = bind(sock, &addr, sizeof(addr));
        if (err < 0) {
                printf("[!] Bind failed\n");
                printf("[!] Emsg: %s\n", strerror(errno));
                return 1;
        }

	
	// construct the "ibuf" for publish()
	struct isockbuf ibuf;
	ibuf.type = TYPE_DATA;
	ibuf.name = name;
	ibuf.nlen = nlen;
	ibuf.data = data;
	ibuf.dlen = dlen;

	// named data will be published and can be access by its name
	err = publish(sock, &ibuf, sizeof(ibuf));
	if (err < 0) {
		printf("[!] publish failed\n");
		printf("[!] Emsg: %s\n", strerror(errno));
		return 1;
        }
	free(data);
	ibuf.data = data = NULL;
	
	// status() will block until request under the prefix comes
	while (err = status(sock, &, sizeof(stat_info), 0)) {
		if (err < 0) {
			printf("[!] status err \n");
			printf("[!] Emsg: %s\n", strerror(errno));
			return 1;
		}
		if (!stat_info.watch_id) {
			// request under a prefix may be satisfied, or not
			printf("[+] stat_info.name_buf = %s \n", stat_info.name_buf);
			printf("    satisfied: %s \n\n", stat_info.satisfied ? "true" : "false");
			if (!stat_info.satisfied) {
				// if request can't be satified, server can send back a NACK 
				char *__data = "APP--NACK";
				size_t __dlen = strlen(__data);
				
				// construct isockbuf for isend()
				struct isockbuf ibuf;
				ibuf.type = TYPE_DATA;
				ibuf.name = stat_info.name_buf;
				ibuf.nlen = strlen(stat_info.name_buf);
				ibuf.data = __data;
				ibuf.dlen = __dlen;
				ibuf.d_type = D_NACK;
				if (isend(sock, &ibuf, sizeof(ibuf), 0) < 0) {
					printf("[!] send NACK failed\n");
					printf("[!] Emsg: %s\n", strerror(errno));
				} else {
					printf("[+] NACK sent \n");
				}
			}	
		}
		memset(stat_info.name_buf, 0, 1024);
		stat_info.nlen = 1024;
	}
        close(sock);
        return 0;
} 
```

As program above shows, server needs to create a socket for communication first, just like some TCP or UDP demos. Then binds this socket with a prefix instead of IP address and port number. Fianlly, publish some content with a name under the prefix. The server can query the status of named data under the prefix, respond unsatisfied requests with NACK. That's quite straightforward. 

Let's take a look NIP's APIs in this program.


##### socket() - API Definition

###### Name

`socket` - NIP socket interface, create an endpoint for communication and returns a file descriptor that refers to that endpoint.

###### Synopsis

```c
#include "isockethdr.h"
int socket (int socket_family, int socket_type, int protocol);
```

###### Parameters: 

- `int socket_family` - (from Linux socket man page)This argument selects the *protocol family* which will be used for communication.These families are defined in <...>. The formats currently understood by the Linux kernel include: **AF_NNET**  
- `int socket_type` - This argument indicates the *socket type*, which specifies the communication semantics. Currently defined types are: 
	- **SOCK_NDP**    
	- **SOCK_RAW** (*Provides raw network protocol access*)  
* `int protocol` - The *protocol* specifies a particular protocol to be used with the socket. The protocols currently includes:  
	- **htons(ETH_P_NIP)**
	- **NIPPROTO_ALL**


###### Return Value 

　　On success, a file descriptor for the new socket is returned. On error, < 0 is returned, and errno is set to indicate the error.


###### Example

```c
sock = socket(AF_NNET, SOCK_NDP, htons(ETH_P_NIP));
sock = socket(AF_NNET, SOCK_RAW, NIPPROTO_ALL);
```

###### Errors　

Socket created failed.  
Emsg：Address family not supported by protocol
　　

##### bind() - API Definition

###### Name

`bind` - bind() assigns a socket address specified by *addr* to the socket referred to by the file descriptor *sock_fd*.  *addrlen* specifies the size, in bytes, of the address structure pointed to by *addr*.

###### Synopsis

```c
#include "isockethdr.h"
int bind(int sock_fd, const struct nsockaddr *addr, socklen_t addrlen);
```
###### Parameters: 

- `int sock_fd` - the file descriptor of socket that is binded to.
- `struct sockaddr *addr` - The definition of the *sockaddr* structure follows.

```c
struct nsockaddr {  /* Note: the max size is 128 Bytes */
	unsigned short int	family;
	__u16 			enable_mask;

	#define BIND_PREFIX		0x0001
	char			*prefix;
	size_t			plen;

	#define BIND_FACEID		0x0002
	__u16			face_id;
};
```

- `socklen_t addrlen` - size of *addr* in bytes.

###### Return Value 

On success, the call returns 0. On error, < 0 is returned, and errno is set appropriately.


###### Example

```c
err = bind(sock, &addr, sizeof(addr));
```

###### Errors　
TODO


##### publish() - API Definition

###### Name

`publish` - Publish a content on a socket with specific name so others can use the name to retrieve the content.

###### Synopsis

```c
#include "isockethdr.h"
int publish(int sock_fd, struct isockbuf *buf, size_t len);    　
```

###### Parameters: 

- `int sock_fd` - the file descriptor of socket  
- `struct isockbuf *buf` - The definition of the *isockbuf* structure follows.

```c
struct isockbuf {
	unsigned int		type;
	char			*name;
	__u16			nlen;
	char			*data;
	__u16			dlen;

	union {
		struct {
			__u8	i_flags; // can_be_prefix, must_be_fresh
			__u32	i_life_time;
			__u64	exp_data_digest;
			__u32	app_params_len;
			void	*app_params;
		};
		struct {
			__u8	d_type; // BLOB, NACK, LINK, KEY
			__u32	fresh_period;
			__u32	sig_len;
			void	*sig;
		};
	};
};
```

- `size_t len` - size of *buf* in bytes.


###### Return Value 

The function returns the number of bytes published, or < 0 if an error occurred.  

###### Example

```c
err = publish(sock, &ibuf, sizeof(ibuf));
```

###### Errors　
TODO

##### status() - API Defination

###### Name

`status` - status() block until new insterest comes, and indicates whether it is satistied.

###### Synopsis

```c
#include "isockethdr.h"
int status(int sock_fd, struct status_info *stat_buf, size_t buf_len, 
		unsigned int flags);  　
```

###### Parameters: 

- `int sock_fd` - the file descriptor of socket  
- `struct status_info *stat_buf` - The definition of the *status_info* structure follows.

```c
struct status_info {
	__u32			watch_id;
	bool			satisfied;

	size_t			nlen;
	char			*name_buf;
};
```

- `size_t buf_len` - size of *status_buf* in bytes.
- `unsigned int flags` - 


###### Return Value 

The function returns <TODO>, or < 0 if an error occurred.  

###### Example

```c
err = status(sock, &stat_buf, sizeof(ibuf), 0);
```

###### Errors　
TODO

##### isend() - API Defination

###### Name

`isend` - send NIP packet specified by *ibuf* to the socket referred to by the file descriptor *sock_fd*.

###### Synopsis

```c
#include "isockethdr.h"
int isend(int sock_fdfd, struct isockbuf *ibuf, size_t len, unsigned int flags);　
```

###### Parameters: 

- `int sock_fd` - the file descriptor of socket  
- `struct isockbuf *ibuf` - The definition of the *isockbuf* structure follows.

```c
struct isockbuf {
	unsigned int		type;
	char			*name;
	__u16			nlen;
	char			*data;
	__u64			dlen;

	union {
		struct {
			__u8	i_flags;
			__u32	i_life_time;
			__u64	exp_data_digest;
			__u32	app_params_len;
			void	*app_params;
		};
		struct {
			__u8	d_type;
			__u32	fresh_period;
			__u32	sig_len;
			void	*sig;
		};
	};
};
```

- `size_t len` - size of *ibuf* in bytes.
- `unsigned int flags` - 


###### Return Value 

The function returns real size sent, or < 0 if an error occurred.  

###### Example

```c
err = isend(sock, &ibuf, sizeof(ibuf), 0);
```

###### Errors　
TODO


#### File Consumer

##### C Program & Comment

```c
// for development with NIP, "isockethdr.h" should be included
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <sys/time.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <linux/isockethdr.h>

#define BUF_SIZE	4096

int main()
{
        int sock, err, tlen, dlen, i;

	// specify a name for request the file
        char *name = "/Rpi-1/lab1038/info/about/10Mfile.txt";
	char *file_name="10Mfile.txt";

	// create an empty file to be written into
	int file_fp;
	char *fname, *_buf;
        size_t nlen = strlen(name), fnlen;
	char *rcv_buf = (char *)malloc(BUF_SIZE);
        if (!rcv_buf) {
                printf("[!] rcv_buf created failed\n");
                return 1;
        }
        memset(rcv_buf, 0, BUF_SIZE);

	file_fp = open(file_name,O_CREAT|O_RDWR,0777);
        if(file_fp < 0) {
         	printf("File:\t%s Can Not Open To Write\n", file_name); 
         	goto err_out; 
        }
	
	// construct the struct 'touch_info' for touch() and request()
	struct touch_info *tinfo;
	tinfo = malloc(1024);
	if (!tinfo) {
                printf("[!] tinfo created failed\n");
                return 1;
        }
	memset(tinfo, 0, 1024);
	tinfo->__realsize = 1024;
	tinfo->nack_clen = (void *)tinfo + tinfo->__realsize - (void *)&tinfo->nack_tinfo;
	tinfo->touchinfo_type = _COMMON_TOUCH_INFO_TYPE;
	tinfo->name = (char *)malloc(nlen);
	if (!tinfo->name) {
                printf("[!] tinfo->name created failed\n");
                return 1;
        }
	memcpy(tinfo->name, name, nlen);
	tinfo->nlen = nlen;

	// create a socket for communication
	// for consumer, there is not need to bind a prefix with socket
        sock = socket(AF_NNET, SOCK_NDP, htons(ETH_P_NIP));
        if (sock < 0) {
                printf("[!] Socket created failed\n");
                goto err_out;
	}

	// touch() will fetch some basic information of named data 
	err = touch(sock, tinfo, tinfo->__realsize, 0);
        if (err < 0) {
                printf("[!] touch failed\n");
                goto err_out;
        } else if (tinfo->nack) {
		printf("[!] touch NACK: %.*s \n", tinfo->nack_clen, nack_tinfo_data(tinfo));
		goto err_out;
	}

	// print basic information of named data
	printf("[+] sizeof(struct touch_info): %u \n", sizeof(struct touch_info));
	printf("[+] copied: %d \n", err);
        printf("[+] tinfo->touchinfo_type: %u \n", tinfo->touchinfo_type);
	printf("[+] cmn_tinfo_basenamelen(tinfo): %u \n", cmn_tinfo_basenamelen(tinfo));
	printf("[+] cmn_tinfo_pin(tinfo):         %.*s \n", __PIN_LEN__, cmn_tinfo_pin(tinfo));
	printf("[+] cmn_tinfo_minsegnum(tinfo):   %u \n", cmn_tinfo_minsegnum(tinfo));
	printf("[+] cmn_tinfo_maxsegnum(tinfo):   %u \n", cmn_tinfo_maxsegnum(tinfo));
	printf("[+] cmn_tinfo_mtu(tinfo):         %u \n", cmn_tinfo_mtu(tinfo));
	printf("[+] cmn_tinfo_pktlen(tinfo):      %u \n", cmn_tinfo_pktlen(tinfo));

	// request() the named data
	err = request(sock, tinfo, tinfo->__realsize);
	if (err < 0) {
                printf("[!] group_request failed\n");
                goto err_out;
        }
	
	// create isockbuf for receive named data
	char name_buf[1024] = { 0 };
	struct isockbuf ibuf;
	ibuf.type = TYPE_DATA;
	ibuf.name = name_buf;
	ibuf.nlen = 1024;
	ibuf.data = rcv_buf;
	ibuf.dlen = BUF_SIZE;
	memset(rcv_buf, 0, sizeof(rcv_buf));

	// data may be splited into segments
	// totally receive (maxsegnum - minsegnum) times
        for (i = cmn_tinfo_minsegnum(tinfo); i < cmn_tinfo_maxsegnum(tinfo) + 1; i ++) {

		// receive named data segment from socket
		err = dlen = irecv(sock, &ibuf, sizeof(ibuf), 0);
                if (err < 0) {
                        printf("[!] irecv failed\n");
                        goto err_out;
                }

		// may receive NACK if named data not exist
		if (ibuf.d_type == D_NACK) {
			int __dlen = ibuf.dlen;
			printf("[DBG] Recv NACK Data, content: %.*s \n", __dlen, ibuf.data);
		} else if (write(file_fp, rcv_buf, strlen(rcv_buf)) < strlen(rcv_buf)) {
           	 	printf("File:\t%s Write Failed\n", file_name); 
        	}
       		memset(name_buf, 0, 1024);
       		memset(rcv_buf, 0, BUF_SIZE);
       		ibuf.nlen = 1024;
       		ibuf.dlen = BUF_SIZE;
        }
	printf("[+] All request() Done! \n");
        close(file_fp);
        close(sock);
	printf("[+] sock closed \n");

        return 0;
err_out:
        printf("[!] Emsg: %s\n", strerror(errno));
	printf("[-] err: %d \n", err);
        return 1;
}
```

Client also needs to create a socket for communication. And then it call touch() the content to get some basic information, including min/max segment number, PIN and content size. Those information are passed out within struct tinfo. Then it pass tinfo into request() to send requests for all the segments of the file. Fianlly, call irecv() to receive every segment of content. In a word, all we need is fetch content by name.

Let's take a look NIP's APIs in this program.

##### touch() - API Definition

###### Name
`touch` -  touch() fetch basic information specified by *tbuf* to the socket referred to by the file descriptor *sock_fd*. *tbuf* specifies the name and touch type, and contains basic information after function successfully returns.

###### Synopsis
```c 
#include "isockethdr.h"
int touch(int sock_fd, struct touch_info *tbuf, size_t buf_size, 
	  unsigned int flags)
```


###### Parameters: 

- `int sock_fd` - the file descriptor of socket.
- `struct touch_info \*tbuf` - The definition of the *touch_info* structure follows.

```c
struct touch_info {
	__u16 			touchinfo_type;
	char			*name;
	__u16			nlen;
	bool			nack;
	__u16			nack_clen;
	__u32			__realsize;
	union {
		struct cmn_tinfo	cmn_tinfo;
		struct gen_tinfo	gen_tinfo;
		struct nack_tinfo	nack_tinfo;
	};
};

struct cmn_tinfo {
	/* Common TouchInfo */
	__u16			base_name_len;
	char			pin[__PIN_LEN__];

	/* Common SegInfo */
	__u32			min_seg_num;
	__u32			max_seg_num;

	__u16			mtu;
	__u16			pkt_len;
};

struct gen_tinfo {
	__u16			tbuf_len;
	char			__first;
};

struct nack_tinfo {
	char			__first;
};
```

- `size_t buf_size` - size of *tbuf* in bytes.
- `unsigned int flags` - usually be 0

###### Return Value 

On success, the call returns 0. On error, < 0 is returned, and errno is set appropriately.


###### Example

```c
err = touch(sock, name, nlen, &tinfo, touch_info_size(tinfo), 0);
err = touch(sock, name, nlen, &tinfo, touch_info_size(tinfo), flags | MSG_DONTWAIT);
```

###### Errors　

TODO


##### request() - API Definition

###### Name
`request` - request a content using the structure *touch_info* returned by touch() which includes the information related to the content.

###### Synopsis

```c 
#include "isockethdr.h"
int request(int sock_fd, struct touch_info *buf, size_t len);
```

###### Parameters: 

* `int sock_fd` - the file descriptor of socket  
* `struct touch_info *buf` - include the information related to the content previously acquired by touch(). The definition of the *touch_info* structure see the definition of touch().
* `size_t len` - size of *buf*  in bytes.

###### Return Value 

On success, the call returns the number of characters received. On error, < 0 is returned, and errno is set appropriately.


###### Example

```c
err = request(sock, tinfo, tinfo->__realsize);
```

###### Errors　
TODO

##### irecv() - API Defination

###### Name
`irecv` -  irecv() receive data from the socket referred to by the file descriptor *sock_fd* and fullfill *buf*.

###### Synopsis
```c
#include "isockethdr.h"
int irecv(int sock_fd, struct isockbuf *ibuf, size_t len, unsigned int flags);
```


###### Parameters: 

- `int sock_fd` - the file descriptor of socket.
- `struct isockbuf *buf` - The definition of the *isockbuf* structure follows.

```c
struct isockbuf {
	unsigned int		type;
	char			*name;
	__u16			nlen;
	char			*data;
	__u16			dlen;

	union {
		struct {
			__u8	i_flags; // can_be_prefix, must_be_fresh
			__u32	i_life_time;
			__u64	exp_data_digest;
			__u32	app_params_len;
			void	*app_params;
		};
		struct {
			__u8	d_type; // BLOB, NACK, LINK, KEY
			__u32	fresh_period;
			__u32	sig_len;
			void	*sig;
		};
	};
};
```

- `size_t len` - size of *buf* in bytes.
- `unsigned int flags` - 

###### Return Value 

TODO


###### Example

```c
err = irecv(sock, &buf, sizeof(buf), 0);
```

###### Errors　

TODO


#### Chat Client

##### C Program with Comment

```c
// for development with NIP, "isockethdr.h" should be included
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <pthread.h>
#include <linux/isockethdr.h>

// for Alice, comment Bob and uncomment Alice
#define Alice
//#define Bob

// define prefix here and the prefix on the other side
#ifdef Alice
	char my_prefix[1024] = "/alice/chat";
	char target_prefix[1024] = "/bob/chat";

#endif // Alice

#ifdef Bob
	char my_prefix[1024] = "/bob/chat";
	char target_prefix[1024] = "/alice/chat";
#endif // Bob

//alice/chat/watchbob/chat/publish/1
void new_msg_ack(int sock, char *name, size_t nlen)
{
	char *data = "OK";
	size_t dlen = strlen(data);

	// construct an isockbuf where contains data as ACK
	struct isockbuf ibuf;
	ibuf.type = TYPE_DATA;
	ibuf.name = name;
	ibuf.nlen = nlen;
	ibuf.data = data;
	ibuf.dlen = dlen;
	
	if (isend(sock, &ibuf, sizeof(ibuf), 0) < 0) {
		printf("[!] new_msg_ack failed\n");
		printf("[!] Emsg: %s\n", strerror(errno));
	}
}

void *receiverLoop(void *arg)
{
	// about this thread:
	// return ACK for reminding from the other side
	// then fetch message published from the other side
	int sock = *(int *)arg;
	
	char name_buf[1024] = { 0 };
	char data_buf[1024] = { 0 };
	struct status_info stat_info;
	stat_info.nlen = 1024;
	stat_info.name_buf = name_buf;

	// construct touch_info for touch()
	struct touch_info *tinfo;
	tinfo = malloc(1024);
	if (!tinfo) {
                printf("[!] tinfo created failed\n");
                return NULL;
        }
	memset(tinfo, 0, 1024);
	tinfo->__realsize = 1024;
	tinfo->nack_clen = (void *)tinfo + tinfo->__realsize - (void *)&tinfo->nack_tinfo;
	tinfo->touchinfo_type = _COMMON_TOUCH_INFO_TYPE;

	// construct isockbuf for receive named data
	struct isockbuf ibuf;
	ibuf.type = TYPE_DATA;
	ibuf.name = name_buf;
	ibuf.nlen = 1024;
	ibuf.data = data_buf;
	ibuf.dlen = 1024;

	// use watch() to register an ID for a subname event 
	int err, watch_id;
	char watch_name[1024] = { 0 };
	sprintf(watch_name, "%s/watch", my_prefix); // /alice/chat/watch
	watch_id = watch(sock, watch_name, strlen(watch_name));
	if (watch_id < 0) {
		printf("[!] watch failed\n");
		printf("[!] Emsg: %s\n", strerror(errno));
		return NULL;
	}
	printf("[DBG] wch_name: %s\n", watch_name);
	
	// status() will block until request under the prefix comes , /alice/chat
	while (err = status(sock, &stat_info, sizeof(stat_info), 0)) {
		if (err < 0) {
			printf("[!] status err \n");
			printf("[!] Emsg: %s\n", strerror(errno));
			return NULL;
		}
		// judge if the request name is watched
		if (stat_info.watch_id == watch_id) { //starts with /alice/chat/watch
			char target_name[1024] = {};
			sprintf(target_name, "%s", &name_buf[strlen(watch_name)]); // /bob/chat/publish/1
			printf("[DBG-rLoop] %d %s\n", strlen(target_name), target_name);

			// return an ACK first
			new_msg_ack(sock, stat_info.name_buf, stat_info.nlen); //alice/chat/watch/bob/chat/publish/1

			// then fetch the message
			tinfo->name = target_name; // /bob/chat/publish/1
			tinfo->nlen = strlen(target_name);
			err = touch(sock, tinfo, tinfo->__realsize, 0);
			err = request(sock, tinfo, tinfo->__realsize);
			err = irecv(sock, &ibuf, sizeof(ibuf), 0);
			printf("[+] From %s: %s\n", (target_prefix+1), data_buf);

			// reset buffer for next loop
			memset(data_buf, 0, sizeof(data_buf));
			ibuf.nlen = 1024;
			ibuf.dlen = 1024;
		}
		memset(name_buf, 0, sizeof(name_buf));
		stat_info.nlen = 1024;
	}
	printf("[!] receiver err out \n");
	pthread_exit(NULL);
}

void *senderLoop(void *arg)
{
	// about this thread:
	// publish new message read from userspace
	// then remind the other side to fetch this message
	int sock = *(int *)arg;
	
	int err;
	char pub_name[1024];
	sprintf(pub_name, "%s/publish", my_prefix); //bob/chat/publish
	printf("[DBG] Start reading\n");
	
	int out_count = 0;
	char str[1024];
	char ack_buf[16];

	struct isockbuf ibuf;
	while (scanf("%s", str)) {
		printf("[DBG-sLoop] %d %s\n", strlen(str), str);
		out_count++;
		char name[1024] = { 0 };
		sprintf(name, "%s/%d", pub_name, out_count); //bob/chat/publish/1
		printf("[DBG-sLoop] %d %s\n", strlen(name), name);

		// construct a isockbuf for cast()
		ibuf.type = TYPE_DATA;
		ibuf.name = name; //bob/chat/publish/1
		ibuf.nlen = strlen(name);
		ibuf.data = str;
		ibuf.dlen = strlen(str);

		// cast() will pulish named data 
		// then automatically unpublish it after a certain time
		err = cast(sock, &ibuf, sizeof(ibuf), 0);
		if (err < 0) {
			printf("[!] cast failed\n");
			printf("[!] Emsg: %s\n", strerror(errno));
			return 1;
		}
		sprintf(name, "%s/watch%s/%d", target_prefix,pub_name, out_count);// alice/chat/watch/bob/chat/publish/1
		printf("[DBG-sLoop] request: %s \n", name);

		// named data has been published. so remain the other side to fetch it
		ibuf.type = TYPE_INTEREST;
		ibuf.nlen = strlen(name);
		err = isend(sock, &ibuf, sizeof(ibuf), 0);
		printf("[DBG-sLoop] print request end\n");
	}
	printf("[!] sender err out \n");
	pthread_exit(NULL);
}

int main()
{
	pthread_t id_recv, id_send;
	void *thres;

	// create a NDP socket for chatting
	int err, sock = socket(AF_NNET, SOCK_NDP, htons(ETH_P_NIP));
	if (sock < 0) {
                printf("[!] Socket created failed\n");
                return -1;
	}

	// in a chat setting, both parties know each other
	// so Both Alice and Bob should bind socket with their prefix
	struct nsockaddr addr;
	addr.family = AF_NNET;
	addr.enable_mask = BIND_PREFIX;
	addr.prefix = my_prefix;
	addr.plen = strlen(my_prefix);
	err = bind(sock, &addr, sizeof(addr));
	if (err < 0) {
                printf("[!] Bind failed\n");
                return -1;
	}
	printf("[INFO] bind [%.*s] ok\n", addr.plen, addr.prefix);
	
	// run receiverloop and senderloop for real time chat
	if (pthread_create(&id_recv, 0, receiverLoop, &sock) != 0) {
		printf("[!] Create thread failed!\n");
		return -1;
	}
	if (pthread_create(&id_send, 0, senderLoop, &sock) != 0) {
		printf("[!] Create thread failed!\n");
		return -1;
	}
	pthread_join(id_recv, &thres);
	pthread_join(id_send, &thres);
	return 0;
}
```

In this chat program, chat client creates a socket in the beginning and bind its prefix with it. Then start 2 threads for receive and send message.

In receiving thread, client register an watch id to watch a certain name prefix. When interest comes for reminding this client, the client will return an ACK for this reminding interest and fetch message from the other side.

In sending thread, client reads message from userspace and call cast() to publish this message for a short time. Then reminds the other side to fetch the message.

Let's take a look NIP's APIs in this program.

##### watch() - API Defination

###### Name

`watch` - watch() will register an ID for a certain name prefix to the socket referred to by the file descriptor *sock_fd*.

###### Synopsis

```c
#include "isockethdr.h"
int watch(int sock_fd, char *name, size_t nlen);
```

###### Parameters: 

- `int sock_fd` - the file descriptor of socket  
- `name` - the name to be watched.
- `nlen` - the length of the name to be watched.


###### Return Value 

The function returns a watch_id on success , or < 0 if an error occurred.  

###### Example

```c
err = watch(sock, name, strlen(name));
```

###### Errors　
TODO


##### cast() - API Defination

###### Name

`cast` - cast() will publish a content for a certain life time then unpublish it automatically.

###### Synopsis

```c
#include "isockethdr.h"
int cast(int sock_fd, struct isockbuf *buf, size_t len, __u32 life_time);

```

###### Parameters: 

- `int sock_fd` - the file descriptor of socket  
- `struct isockbuf *buf` - The definition of the *isockbuf* structure follows.

```c
struct isockbuf {
	unsigned int		type;
	char			*name;
	__u16			nlen;
	char			*data;
	__u16			dlen;

	union {
		struct {
			__u8	i_flags; // can_be_prefix, must_be_fresh
			__u32	i_life_time;
			__u64	exp_data_digest;
			__u32	app_params_len;
			void	*app_params;
		};
		struct {
			__u8	d_type; // BLOB, NACK, LINK, KEY
			__u32	fresh_period;
			__u32	sig_len;
			void	*sig;
		};
	};
};
```

- `size_t len` - size of *buf* in bytes.
- `__u32 life_time` - life time of the content to be published.


###### Return Value 

The function returns the number of bytes published, or < 0 if an error occurred.  


###### Example

```c
err = cast(sock, &ibuf, sizeof(ibuf), 0); // life_time = 0, means default 4s
err = cast(sock, &ibuf, sizeof(ibuf), 1000); // cast the content for 1 second
```

###### Errors　
TODO


### Resources

Below are some useful resources:
- [Application Demos](http://link)
- [BLV packet format Specification](http://link)
- [APIs Referrence](http://link)
- [How NIP works](http://link)
- [Download Center](http://link)

- [Website](http://link)





# 学习笔记

## 1.Filetransfer

1. provider和consumer应该使用同一种包格式，BLV或TLV。
2. 可以使用dmesg打印内核输出的信息
3. 可以在nip-kmod文件夹下编辑config.h文件，修改配置后make clean，make，编译后重新插入ko模块。
4. provider将文件内容publish后，内容被复制到内核空间，由内核来负责回复interest包。

