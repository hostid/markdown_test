
##编程命名/编程思想

[TOC]

### 一、编程命名

**C风格**
> 文件命名： font.c  key_manage.c  wifi_config.h
> 全局变量命名： g_u8value(偏向：g指的global,且该命名方式标注了字节大小)  、g_value
> 局部变量命名： i_u8Value、ivalue、value(推荐：不加g修饰默认都是局部)     //i: interal表示内部，局部变量 

```
//结构体定义：
//T: typedef 表示该数据类型时一个typedef的类型
//PT ： point  typedef
typedef struct FontOpr {
	char *name;
	int (*FontInit)(char *pcFontFile, unsigned int dwFontSize);
	int (*GetFontBitmap)(unsigned int dwCode, PT_FontBitMap ptFontBitMap);
	struct FontOpr *ptNext;
}T_FontOpr, *PT_FontOpr;

typedef struct {  //这里也可以不加
	char *name;
	int (*FontInit)(char *pcFontFile, unsigned int dwFontSize);
	int (*GetFontBitmap)(unsigned int dwCode, PT_FontBitMap ptFontBitMap);
	struct FontOpr *ptNext;
}T_FontOpr, *PT_FontOpr;


T_FontOpr g_tFontOpr;  //g:全局的，t:这是个结构体类型的。
```

> 函数定义： WrireByte()；

**C++风格**

```
class  SeriPort  //定义类
{
  int WriteByte()
    {
	int tmpValue;
  	int data;
    }
}

int main()
{
  SeriPort  ctrUart = new SeriPort();
  ctrUart->WriteByte();		
}
```

**驱动层编程风格(下划线大行其道)**
> 文件命名： font.c  key_manage.c  wifi_config.h
> 全局变量命名： g_write_value;
> 局部变量命名： i_write_value、ivalue、value(不加g修饰默认都是局部)
>  函数定义： writee_byte()；





###二、 编程思想
>思想就像是现实    第一时间你能想到的就是思想   告诉你一写条件   让你写出这个程序        你脑子第一个反应过来的就是思想         比如有人让你去做一顿饭        那你肯定第一想到的就是怎么做这顿饭     顺序    每一步需要什么   这就是对于编程的大概思想   我不能说我的就是对的,     但我是这样认为的       每个人的想法都不同     网上说敲个几万行就什么都清楚了,肯定也是有一定道理的     当你敲到一定程度的时候   你就



***重点，以C++面相对想的思想编程去编程C,这个也是自己后期主要采用的编程思想。*** 

概要：对于软件的相关变量，函数执行体，最终都是封装与一个大的最外层的结构体。该结构体中可能包含这子结构体，包含这指针函数，那么对于相关的操作，编程时自动权衡下面的的利弊关系。

思想一、

```
typedef strtct
{
	int flag;
}  T_base;

typedef  struct
{
  int b;
  T_base  ibase;
}T_MAX;  //最外层最大的结构体
```

```
操作方式一、
main()
{
	T_base mybase;
	T_MAX  mymax;
	my_base.flag = 1;
	mymax.ibase = mymax;

  if(mymax.ibase .flag == 1)
  {
		//这里比方说，要改变，flag的值。
		mymax.ibase .flag == 0;
		//根据该编程思想我们每次操作的都是最外层最大的结构体，下次有程序需要该标志位判断时直接也应该时判断的mymax.ibase .flag的值。
}
  if(mymax.ibase .flag == 0)
  {
		//DO Other something 
}

}
```

> 总结：这样难道不觉的繁琐吗，既然你这样做那定义T_base mybase这个结构体有和意义，它知识说在main函数初始化的时候，把相关的初始值付给了最大的结构体内部。最后根据标志位修改时，你去修改哪个结构体mybase、mymax.ibase？修改任何一个都是独立的，不会改变两一个的值。根据该编程思想我们每次操作的都应是最外层最大的结构体，下次有程序需要该标志位判断时直接也应该时判断的mymax.ibase .flag的值。或者你当然也可以执行这样的操作，但是你自己看这样烦不烦有何意义？

```
main()
{
	T_base mybase;
	T_MAX  mymax;
	my_base.flag = 1;
	mymax.ibase = mymax;

  if(mymax.ibase .flag == 1)
  {
		mymax.ibase .flag == 0;
		my_base.flag = 0;
}
}
```

```
操作方式二、

//不需要还去定义一个T_base，直接把其定义到内部  T_base  ibase，并且操作的都是内部这个。
main()
{
	T_MAX  mymax;
	mymax.my_base.flag = 1;
  if(mymax.my_base.flag  .flag == 1)
  {
		//do something
}
}

存在问题:如果最外层操作结构体T_MAX  mymax内部封装的子结构体潜逃很长，那么按照操作方式二的话编写程序的化，代码看上去不太美观。

比如:

staru   a
{
	   int  a;
}
struct  b
{
  staru   a;
  int b;  
}
struct  C
{
  staru   C;
  int C;  
}
struct  MAX
{
  staru   C;
}

main()
{
	struct  MAX  mymax;
	//要去操作最低端
	mymax.c.b.a.a = xx;  //看上去就很烦。
}
//适用场合: 嵌套不多的情况
```

思想二、
```
typedef strtct
{
	int flag;
}  T_base，*PT_base;

typedef  struct
{
  int b;
  PT_base  p_ibase;
}T_MAX;  //最外层最大的结构体
```

```
操作方式一、
main()
{
	T_base mybase;
	T_MAX  mymax;
	my_base.flag = 1;
	mymax.p_ibase= &mybase;

  if(mymax.ibase .flag == 1)
  {
		//这里比方说，要改变，flag的值,我们就可以操作标志等变量时，直接操作的时本结构体的内部自己的变量，并且它自己的改变会导致最外层全局操作的结构体内容对应的子结构体也会改变.
    my_base.flag = 0;
		
	}
  if(mymax.p_ibase == 0)  //由最外层的结构体去判断
  {
		  //DO SOMEEHING
  }
}

使用注意事项： 指针段错误问题

与方法一比较：不繁琐，节省内存mybase+p_ibase，一个结构体一个指针，而方式1外部内部2个结构体。
与方法二比较：不繁琐，代码看上去很好，分结构体操做。理解性好。但是要注意指针用的不正确的的问题，容易引起段错误。
```
###三、 编程之软件分层架构
 //分层去编写每一层的代码，各层之间通过某种结构进行连接通信。
Platform层：
协议层：
硬件层：



##附录--函数指针/数组指针/----
https://blog.csdn.net/u013719339/article/details/80563247

https://www.cnblogs.com/tp-16b/p/7860491.html