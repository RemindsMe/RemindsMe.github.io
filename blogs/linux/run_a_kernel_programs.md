### 运行一个内核模块程序

内核模块时指的是在操作系统内核中动态加载的一段代码，它可以扩展和增强操作系统的功能。内核模块通常用于为操作系统添加新的设备驱动程序、文件系统、网络协议栈等功能。

内核模块是以二进制形式存在的（`*.ko`），它们被编译为对象文件，并在运行时被加载到操作系统内核中。内核模块与操作系统内核紧密耦合，可以访问内核的数据结构、函数和服务，并与操作系统的其他部分进行交互。

编写一个`hello_kernel.c`程序：

```c
#include <linux/init.h>
#include <linux/module.h>

static int __init hello_init(void) {
    printk(KERN_INFO "Hello, World!\n");
    return 0;
}

static void __exit hello_exit(void) {
    printk(KERN_INFO "Goodbye, World!\n");
}

module_init(hello_init);
module_exit(hello_exit);

MODULE_AUTHOR("Hong");
MODULE_DESCRIPTION("A simple hello world module");
MODULE_VERSION("0.1");
```

其中，`module_init()`在该内核模块加载时被调用，`module_exit()`在退出时调用。

这段程序需要通过`make`命令来进行编译，可以创建一个`Makefile`文件：

```makefile
obj-m := hello_kernel.o
KERNEL := /usr/src/kernels/3.10.0-1062.9.1.el7.x86_64/
PWD := $(shell pwd)

modules :
        make -C $(KERNEL) M=$(PWD) modules

.PHONEY : clean

clean :
        rm -rf *.o *.ko
```

几个重要字段含义：

- `obj-m`：指定要编译的源码；
- `modules`：目标规则，即执行`make modules`时的编译规则；
- `clean`：目标规则，即执行`make clean`时的编译规则，用于清除编译后的内核模块；
- `.PHONEY`：用来声明一些伪目标（phony targets），这些目标不对应实际的文件名，而是表示一个动作或命令，通常用于定义一些不需要生成对应输出文件的规则，或者用于避免与同名文件产生冲突。例如：clean、install、test 等等。

在终端执行编译指令，如果没有语法错误，可以得到编译好的内核模块`hello_kernel.ko`。

```shell
make modules
```

执行下面指令，操作内核模块：

```shell
# 加载内核模块
insmod hello_kernel.ko
# 列出并检索内核模块
lsmod | grep hello_kernel
# 移除内核模块
rmmod hello_kernel
```

可通过以下方式查看`printk()`的输出内容：

```shell
# 1 - 查看 messages 日志
tail -f /var/log/messages

# 2 - 查看 dmesg
dmesg
```
<br>

{% include disqus.html %} 

<br>
