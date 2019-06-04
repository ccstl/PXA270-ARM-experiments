# PXA270-ARM-experiments
ARM嵌入式实验

# 一、	实验目的
+ 加深对嵌入式系统及其相关基础知识的理解。
+ 了解并使用简单的linux命令。
+ 了解宿主PC机与PXA270目标版，能正确连接宿主PC机与PXA270目标版。
+ 学会在宿主机上安装Linux操作系统。
+ 学会建立宿主PC机端的开发环境。
+ 学会配置宿主PC机端的超级终端。
+ 配置宿主PC机端的TFTP服务，并开通此服务 。
+ 配置宿主PC机端的NFS服务，并开通此服务。
+ 学会简单Linux驱动程序的设计。

# 二、	试验箱 
![pic](https://github.com/wolfbrother/PXA270-ARM-experiments/blob/master/_pictures/pic1.jpg?raw=true)
# 三、	实验内容

## （一）	基本实验
实验一到六为基础实验， 主要是为了在熟悉实验操作平台的同时为后续实验搭建好软、硬件环境，配置好相关的协议、服务。

#### 实验一：完成各个硬件的互联，搭建好了实验的硬件环境。

#### 实验二：是在宿主PC端安装虚拟机，提供了实验需要的Linux操作系统,
整体感觉实验室的台式机运行速度很慢，安装软件就进行了两个多小时。做得慢的同学第一次实验课仅仅将软件安装成功而已。 

#### 实验三：是宿主PC端开发环境的安装与配置。

#### 实验四：是配置宿主PC机端的超级终端，使PC机与PXA270目标板之间可以通过串口通讯。
 
#### 实验五：是配置宿主PC机的TFTP服务。TFTP是简单文件传输协议。每次重启宿主PC机时，都要重启该服务，重启命令为：

> service xinetd restart。

配置完成之后，为了简单测试一下TFTP是否可用，要输入一下5条命令：

```
① ifconfig eth0 192.168.0.50 up
② cp /pxa270_linux/IMAGE/zImage /tftpboot/ -arf
③ tftp 192.168.0.100
④ tftp>get zImage
⑤ tftp>q
```

#### 实验六:是配置宿主PC机端NFS服务。

NFS是指网络文件系统，它实现了文件在不同的系统间使用。当使用者想用远端档案时，只需调用“mount”就可以远端系统挂接在自己的档案系统之下。每次重启宿主PC机时，也都要重启该服务，重启命令为:
```
service nfs restart
service nfs restart
```
## (二)基本接口实验
#### 实验十二:简单设备驱动程序

实验目的:动手实践一个简单的字符型设备驱动程序，学习Linux驱动程序构架，学习在应用程序中调用驱动。

实验内容：编写简单的字符型设备驱动程序，编写相应的应用程序。

本实验程序比较简单，具体见教材，这里不再附带代码。驱动程序pxa270_hello_drv.c和Makefile两个文件编辑完成后，用make modules编译驱动程序，编写测试文件simple_test_driver.
c，然后GCC编辑器编译测试程序生成测试文件。成功生成测试文件后用超级终端开始挂载，加载驱动程序，使用命令./test测试，观察测试结果，实验完成。

操作过程如下图所示。
 
驱动程序编辑示意图

 
编辑makefile文件

 
编译驱动程序成功，如图，系统提示无误。


 
编译驱动程序

 
将宿主机的根目录挂载到目标板的mnt目录下
 
运行成功示意图
实验总结：
		通过本实验，我们编写的测试程序能够正常地对驱动程序进行操作，就表示驱动程序能正常工作，当然真正的驱动程序会对应特定的硬件，测试程序就相应地复杂多了。在以后的实验中，我们会开始编写真正对应实际硬件的驱动程序。
		然而，本次试验无论实在软件思想上还是操作方法上都极具代表意义。顺利经过这一关，后面更复杂的试验就不再那么可怕。



#### 实验十三：CPU GPIO驱动程序设计

实验目的：
编写针对实际硬件的驱动程序，进一步了解驱动框架。

实验内容：
+ 1、编写PXA270 GPIO驱动程序和应用程序。
+ 2、在LINUX系统中插入自己的驱动程序，调用它。实现用CPU GPIO控制外部LED，利用PXA270核心板上的LED验证我们的工作。

驱动程序代码：
驱动程序代码与实验十二无大区别，下面列出需要补充的代码。

补充代码①：
```c
#ifdef OURS_GPIO_LED_DEBUG
		printk("SIMPLE_GPIO_LED_write[--kernel--]\n");
	#endif
	
	return count;
```
补充代码②:
```c
#ifdef OURS_GPIO_LED_DEBUG
	printk("SIMPLE_GPIO_LED_open[--kernel--]\n");
#endif
MOD_INC_USE_COUNT;
return 0;
```
补充代码③：
```c
open:	SIMPLE_GPIO_LED_open,
read:	SIMPLE_GPIO_LED_read,
write:	SIMPLE_GPIO_LED_write,
ioctl:	SIMPLE_GPIO_LED_ioctl,
release:	SIMPLE_GPIO_LED_release,
```

Makefile文件补充代码：
将实验十二相关代码作如下修改即可：
```c
TARGET = pxa270_gpio_led_drv.o
modules: $(TARGET)

all: $(TARGET)

pxa270_gpio_led_drv.o:pxa270_gpio_led_drv.c
	$(CC) -c $(CFLAGS) $^ -o $@
```

编译运行和实验十二相同，不再赘述。

运行验证过程如下图所示：
 
驱动成序编辑过程剪影
 
编译测试程序

 
运行成功，在试验箱上可以看到绿色LED灯闪烁。
作业题代码:
原测试文件中是让LED灯亮一秒灭一秒。作业中要求将LED灯改为亮7秒，灭5秒的效果。修改代码如下：
```c
while(1)
{	ioctl(fd, LED_OFF);
sleep(5); 					// 灭5秒
ioctl(fd,LED_ON);
sleep(7);	}				// 亮7秒
}
```

注意：因为作业题与前边处代码有些许不同，其他均完全相同，在此操作方法可参见前边的图，不再赘述。验证结果和要求符合得很好。














#### 实验十四：中断实验
实验目的：学习中断的相关概念，以及Linux系统是如何处理中断的，并且学会编写获取和处理外中断的驱动程序。
实验内容：编写获取和处理中断的驱动程序。
驱动程序代码：
补充代码①：
```c
	printk("***************************************");
	printk("\t %s \t\n",VERSION);
	printk("***************************************");
```

补充代码②：
```c
#ifdef OURS_INT_DEBUG
		printk("SIMPLE_INT_read[--kernel--]\n");
#endif
return count;
```

补充代码③：
```c
#ifdef OURS_INT_DEBUG
		printk("SIMPLE_INT_write[--kernel--]\n");
#endif
return count;
```

补充代码④：
```c
open:	SIMPLE_INT_open,
read:	SIMPLE_INT_read,
write:	SIMPLE_INT_write,
ioctl:	SIMPLE_INT_ioctl,
release:	SIMPLE_INT_release,
```

Makefile文件如实验十三做相应修改，不再赘述。

本实验不需要测试程序。因为中断是一个异步的外部事件，要求每到中断发生的时候，系统都需要响应，所以不需要测试程序，系统也会自动响应中断的。
驱动程序编译成功后，直接用超级终端挂载，按目标版键盘上方的SW2键就会触发中断。
实验过程截图如下：
 
编译驱动程序无误、
 
运行成功，中断能够很好地响应。

#### 实验十五、数码管显示驱动实验
本实验中，我们要编驱动程序以实现在Linux系统下控制LED数码管的显示。

驱动程序代码：

补充代码①：
```c
printk("***************************************");
printk("\t %s \t\n",VERSION);
printk("***************************************");
```

补充代码②：
```c
#ifdef OURS_SERIAL_LED_DEBUG
		printk("SERIAL_LED_read[--kernel--]\n");
#endif
return count;
```

补充代码③：
```c
#ifdef OURS_ SERIAL_LED_DEBUG
		printk(" SERIAL_LED_write[--kernel--]\n");
#endif
return count;
```

补充代码④：
```c
#ifdef OURS_ SERIAL_LED _DEBUG
		printk(" SERIAL_LED _ioctl[--kernel--]\n");
#endif
return 0;
```

补充代码⑤：
```c
#ifdef OURS_ SERIAL_LED _DEBUG
		printk(" SERIAL_LED _open[--kernel--]\n");
#endif
MOD_INC_USE_COUNT;
return 0;
```

补充代码⑥：
```c
#ifdef OURS_ SERIAL_LED _DEBUG
		printk("SERIAL_LED _release[--kernel--]\n");
#endif
MOD_DEC_INC_USE_COUNT;
return 0;
```

补充代码⑦：

```c
open:	SERIAL_LED _open,
read:	 SERIAL_LED _read,
write:	SERIAL_LED _write,
ioctl:	SERIAL_LED _ioctl,
release:	SERIAL_LED _release,
```

补充代码⑧：
```c
int ret = -ENODEV;
		ret = devfs_register_chrdev(SERIAL_LED_MAJOR,  "serial_led_ctl",& SERIAL_LED _ops);
		showversion();
		if(ret<0)
		{
			printk("pxa270 init_module failed with %d \n[--kernel--]",ret);
		}
		else
		{
			printk("pxa270 serial_led_driver register success!!![--kernel--]\n");
		}
return ret;
```

补充代码⑨：
```c
int ret = -ENODEV;
		#ifdef OURS_SERILA_LED_DEBUG
			printk("pxa270_ SERIAL_LED_init[--kernel--]\n");
		#endif  
    	ret = HW_ SERIAL_LED_init();
    	if (ret)
return ret;
return 0;
```

补充代码⑩：
```c
#ifdef OURS_SERIAL_LED_DEBUG
		printk("cleanup_SERIAL_LED_ctl[--kernel--]\n");
#endif
devfs_unregister_chrdev (SERIAL_LED_MAJOR, "serial_led_ctl" );
```

补充代码⑾：
```c
MODULE_DESCRIPTION("simple serial led driver module");
MODULE_AUTHOR("liduo");
MODULE_LICENSE("GPL");
module_init(pxa270_SERIAL_LED_init);
module_exit(cleanup _SERIAL_LED_ctl);
```

Makefile文件与实验十四相同，只需作相应修改即可，不再赘述。

具体操作过程如下：

 
编译驱动程序

 
运行测试程序成功
作业题代码：

+ 1、	实现目标板上LED数码管循环显示数字9-0。
将原测试程序相关代码修改如下：
```c
while(1)
{ 	for(count=9;count>=0;count--)			               // 倒序显示数字
{ 	data[0] = buf[count]; ret=write(fd,data,1); sleep(1);
}
}
```

+ 2、	实现目标板上LED数码管循环显示数字2、4、6、8、0或8、6、4、2、0。
将原测试程序相关代码修改如下：
```c
while(1)
{ 	for(count=0;count<9;count=count+2)		              // 只循环显示偶数
{ 	data[0] = buf[count]; ret=write(fd,data,1); sleep(1);
}
}
```

注意：操作方法同上，LED显示符合要求，已由黄老师验收过，故此处仅陈列修改的测试程序代码部分。











#### 实验十六、LED点阵驱动程序设计
实验目的：学会编写驱动程序，实现在Linux系统下控制LED点阵显示，并在此基础上稍加改进，实现对LED的控制。

实验内容：编写一个针对硬件LED点阵的驱动程序。

驱动程序代码：

补充代码①：
```c
printk("***************************************");
printk("\t %s \t\n",VERSION);
printk("***************************************");
```
补充代码②：
```c
#ifdef OURS_LED_DEBUG
		printk("SIMPLE_LED_read[--kernel--]\n");
#endif
return count;
```
补充代码③：
```c
#ifdef OURS_LED_DEBUG
		printk("SIMPLE_LED_ioctl[--kernel--]\n");
#endif
return 0;
```
补充代码④：
```c
open:	SIMPLE_LED_open,
read:	SIMPLE_LED_read,
write:	SIMPLE_LED_write,
ioctl:	SIMPLE_LED_ioctl,
release:	SIMPLE_LED_release,
```
补充代码⑤：
```c
int ret = -ENODEV;
		#ifdef OURS_LED_DEBUG
			printk("pxa270_LED_init[--kernel--]\n");
		#endif  
ret = HW_ LED_init();
if (ret)
	return ret;
return 0;
```
补充代码⑥：
```c
#ifdef OURS_LED_DEBUG
		printk("cleanup_LED_ctl[--kernel--]\n");
#endif
devfs_unregister_chrdev (SIMPLE_LED_MAJOR, "led_ctl" );
```

Makefile程序仍然可以用前一个实验的，只要把相关函数名改了就可以，此处不再赘述。
 
编辑驱动程序过程截图
 
运行成功


+ 作业题代码：

原测试程序中有如下代码：
```c
for (i=1;i<=8;i++)
{ 	buf[0]=c;
 buf[1]=~r; 		// row
for (j=1;j<=8;j++) 
{	  
write(fd,buf,2);
printf ("buf[0],buf[1]: [%x,%x]\n",buf[0],buf[1]);
usleep(200000); 		// sleep 0.2 second 
c = c<<1;
buf[0]=c; 			// column
}
c = 1;
r = r<<1;
}
```

通过for循环嵌套，列向量左移8次后行向量才移1位，实现了点阵横向逐行扫描。
 
 横向扫描作业题代码：
+ 1、	作业题要求实现按横的方向隔行顺序扫描LED点阵数码管，修改上段代码如下;

```c
for (i=1;i<=4;i++)				// 修改外层循环次数为4
{ 	buf[0]=c; buf[1]=~r;
for (j=1;j<=8;j++) 
{	write(fd,buf,2);
printf ("buf[0],buf[1]: [%x,%x]\n",buf[0],buf[1]);
usleep(200000); 		// sleep 0.2 second 
c = c<<1;
buf[0]=c;
}
c = 1;
r = r<<2;					// 行向量每次移2位,实现隔行扫描
}
```

+ 2、	作业题要求实现按竖的方向顺序扫描LED点阵数码管，修改原测试程序代码如下：
```c
for (i=1;i<=8;i++)                          //外层循环次数不变仍为8
{ 	buf[0]=c; buf[1]=~r;
for (j=1;j<=8;j++) 
{	write(fd,buf,2);
printf ("buf[0],buf[1]: [%x,%x]\n",buf[0],buf[1]);
usleep(200000); 		// sleep 0.2 second 
r= r<<1;				// 修改内循环为对行向量移位
buf[1]=~r;
}
r = 1;
c = c<<1;					   // 修改外循环为对列向量移位
}
```











#### 实验十七：AD驱动实验
实验目的：编写驱动程序对模拟量输入进行采集，并转换为数字量显示在超级终端上，从而实现AD转换。

实验内容：

+ 1、	编程对模拟量输入进行采集和转换，并将结果显示在超级终端上。
+ 2、	通过改变模拟量输入，观察显示结果。

驱动程序代码：

补充代码①：
```c
printk("***************************************");
printk("\t %s \t\n",VERSION);
printk("***************************************");
```
补充代码②：
```c
#ifdef OURS_ADCTL_DEBUG
		printk("SIMPLE_ADCTL_read[--kernel--]\n");
#endif
return count;
```

补充代码③：
```c
#ifdef OURS_ADCTL_DEBUG
		printk("SIMPLE_ADCTL_write[--kernel--]\n");
#endif
return count;
```

补充代码④：
```c
#ifdef OURS_ADCTL_DEBUG
		printk("SIMPLE_ADCTL_open[--kernel--]\n");
#endif
MOD_INC_USE_COUNT;
return 0;
```

补充代码⑤：
```c
#ifdef OURS_ADCTL_DEBUG
		printk("SIMPLE_ADCTL_release[--kernel--]\n");
#endif
MOD_DEC_INC_USE_COUNT;
return 0;
```

补充代码⑥：
```c
open:	SIMPLE_ADCTL_open,
read:	SIMPLE_ADCTL_read,
write:	SIMPLE_ADCTL_write,
ioctl:	SIMPLE_ADCTL_ioctl,
release:	SIMPLE_ADCTL_release,
```

补充代码⑦：
```c
int ret = -ENODEV;
ret = devfs_register_chrdev(SIMPLE_ADCTL_MAJOR,  "adctl_serial_ctl",& ADCTL_ops);
showversion();
if(ret<0)
		{
			printk("pxa270 init_module failed with %d \n[--kernel--]",ret);
		}
else
		{
			printk("pxa270 adctl_driver register success!!![--kernel--]\n");
		}
return ret;
```

补充代码⑧：
```c
int ret = -ENODEV;
#ifdef OURS_ADCTL_DEBUG
		printk("pxa270_ ADCTL_init[--kernel--]\n");
#endif  
ret = HW_ ADCTL_init();
if (ret)
  		return ret;
return 0;
```

补充代码⑨：
```c
#ifdef OURS_ADCTL_DEBUG
		printk("cleanup_ADCTL_ctl[--kernel--]\n");
#endif
devfs_unregister_chrdev (SIMPLE_ADCTL_MAJOR, "adctl_ctl" );
```

Makefile文件可以用前一个程序的文件，只要将相应部分的代码修改即可，此处不再赘述。
作业题代码：
```c
for(i=0;i<50;i++)
{	Val0 = ioctl(fd,UCB_ADC_INP_AD1,0);
usleep(100);
Val1 = ioctl(fd,UCB_ADC_INP_AD0,0); 
usleep(500000);
}
```

作业题要求将UCB_ADC_INP_AD0换成其他通道，但由于AD通道只有AD0和AD1，故只是将原测试程序代码中的AD0与AD1对换。

#### 实验十八：DA驱动实验
实验目的：了解数模转换的基本原理，掌握数模转换的编程方法。
实验内容：编写驱动程序，实现将数字信号转换成模拟信号并在示波器上显示出模拟信号波形，即实现DA转换。
驱动程序代码：
补充代码①——头文件
```c
#include <linux/config.h>
#include <linux/kernel.h>
#include <linux/sched.h>
#include <linux/timer.h>
#include <linux/init.h>
#include <linux/module.h>
#include <asm/hardware.h>
#include<asm/io.h>
```

补充代码②：
```c
printk("***************************************");
printk("\t %s \t\n",VERSION);
printk("***************************************");
```

补充代码③：
```c
#ifdef OURS_DA_DEBUG
		printk("SIMPLE_DA_read[--kernel--]\n");
#endif
return count;
```

补充代码④：
```c
#ifdef OURS_DA_DEBUG
		printk("SIMPLE_DA_write[--kernel--]\n");
#endif
return count;
```

补充代码⑤：
```c
#ifdef OURS_DA_DEBUG
		printk("SIMPLE_DA_ioctl[--kernel--]\n");
#endif
return 0;
```

补充代码⑥：
```c
#ifdef OURS_DA_DEBUG
		printk("SIMPLE_DA_open[--kernel--]\n");
#endif
MOD_INC_USE_COUNT;
return 0;
```

补充代码⑦：
```c
open:	SIMPLE_DA_open,
read:	SIMPLE_DA_read,
write:	SIMPLE_DA_write,
ioctl:	SIMPLE_DA_ioctl,
release:	SIMPLE_DA_release,
```

补充代码⑧：
```c
int ret = -ENODEV;
ret = devfs_register_chrdev(SIMPLE_DA_MAJOR,  "da_serial_ctl",& DA_ops);
showversion();
if(ret<0)
{
	   printk("pxa270 init_module failed with %d \n[--kernel--]",ret);
}
else
{
	   printk("pxa270 DA_driver register success!!![--kernel--]\n");
}
```


补充代码（9）：
```c
int ret = -ENODEV;
#ifdef OURS_DA_DEBUG
	printk("pxa270_ DA_CTL_init[--kernel--]\n");
#endif  
ret = HW_ DA_CTL_init();
if (ret)
	return ret;
return 0;
```

补充代码⑩：
```c
#ifdef OURS_DA_DEBUG
		printk("cleanup_DA_ctl[--kernel--]\n");
#endif
devfs_unregister_chrdev (SIMPLE_DA_MAJOR, "da_ctl" );
```

补充代码⑾：
```c
MODULE_DESCRIPTION("simple da driver module")；
MODULE_AUTHOR("phy");
MODULE_LICENSE("GPL");
module_init(pxa270_DA_init);
module_exit(cleanup _DA_ctl);	
```

Makefile文件可以继续用前面程序Mekefile的代码，只需要将相应部分的代码修改即可，在此不再赘述。
作业题代码：
作业题要求我们编写一个输出三角波的程序，由于原测试程序中已有产生正弦波和方波的代码，此处我们只要将原测试程序中产生正弦波的代码改成产生三角波的代码即可。

三角波抓拍

原正弦波代码：
x=sin((j/POINT)*(2*M_PI));
改成产生三角波的代码为：
x=((j/POINT)*(5*M_PI));
至此我们的嵌入式实验已全部完成。

         









# 四、	实验总结
在本次嵌入式实验中，我们首先在老师的指导下了解了嵌入式系统，初步接触了Linux环境。我们的实验板是OURS-PXA270-EP，它是一款基于INTEL XSCALE PXA270处理器，针对高校嵌入式系统教学和实验科研的平台。这款设备主要包括核心板与底板两个部分，核心板主要集成了高速的PXA270 CPU,配套的存储器，网卡等设备；底板主要是各种类型的接口与扩展口。

了解了实验的平台后，在接下来的基本实验中我们学会了嵌入式开发系统硬件环境的搭建、Linux操作系统RedHat9的安装、软件环境的搭建，以及配置超级终端，配置通讯服务。这些实验内容只要按照实验指导书上的步骤一步一步做即可，不会出现难以解决的问题，一般都会做的很顺利。
基本试验之后就是基本接口实验，在做该部分的实验之前，我们需要重启基本实验中我们配置好的服务，重新挂载我们配置好的超级终端。该部分实验主要是要我们学会简单驱动程序的编写，其实按照书上的要求做，也不是很难，但是，该部分实验要输入的代码比较多，在输入的时候很容易出现格式错误，所以需要十二分的细心。

基本接口实验每个实验后面的作业题应该是实验中较难的部分了，但是只要看懂了书上已给出的测试程序的代码，再在此基础上修改即可，一般只需修改一两行代码，只要认真做就不是特别困难。

最让我难忘的是第12节实验课。不知是那儿不对，那个代码我输入了三遍，浪费了很长时间，以至于落后别人一大截。还好最后越过了这个坎。后来就再也没有遇到这种情况，所以后面几个实验做得很顺利，最终我还是赶上了大部队。

很多同学对我们的实验课认识不足，心里不重视，觉得这种课很水，就是一点儿不做也能得到不错的分数；语气在实验室埋头苦干，倒不如努力学习微机原理等难学然而学分很高的课程。我觉得这就有点儿本末倒置了。学校每年对实验课的投入很大，包括实验器材的购买，教师的培训等，往往一掷千金。从这一点可以看出学校、社会对实验课有多么重视，实验课有多么重要。我们学习的最终目的，不是看你考了多少多少分，而是看你能做多少事，看你动手能力有多强。很多学生考试成绩很好，但是动起手来则表现一塌糊涂，实在是不应该。所以我觉得实验课应当切实重视起来，只有这样我们才能均衡发展，成长为对社会有用的人才。





