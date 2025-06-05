> 这已经是十分古老的技术了，看看就好，知道怎么写就算成功

## 第一章 通用编程技术
![[Pasted image 20250525081413.png]]

### 神奇的数据驱动设计
> Author: Steve Rabin
> Contact: 

游戏由两部分组成：**逻辑和数据**，这是一种有效的划分。分开来，它们一点用处都没有，不过合在一起，它们就能让你的游戏充满生机。
逻辑部分定义游戏引擎的核心原则合算法，数据部分则提供其内容合行为的具体细节。当逻辑合数据互相分离开来，并能够独立发展，则会产生神奇的效果。
游戏数据应该从文件载入，而不是应该内嵌在代码中。如何适度把握其中的平衡是问题的关键。

#### 1 基础
创建一个能按需解析文本文件的系统。这对于数据驱动设计的有效应用来说至关重要。
文本文件如果只用作编辑修改是没有什么实际意义的，它的真正意义在于——不用修改一行代码，就可以进行各种新的尝试，并改变不同的东西来进行测试。
#### 2 最低标准
不要编码常量；把常量放进文本文件中，这样进行改变时就不需要重新编译代码了。如果处理得当，那么所有人都可以使用一下记事本就能够改变摄像机行为。
游戏设计者合制作者通常会受控于程序员，通过报复算法常量，那些非程序员们可以用各种值来进行调试以达到他们的确切行为，而不需要打扰任何一个程序员。
#### 3 杜绝硬编码
要假定任何东西都可能改变。如果游戏调用屏幕拆分，**不要用硬编码来实现**。只要采用正确的设计，并不会增加多少工作量就可以有一个可维护性的摄像机。
如果核心设计决策很灵活，游戏就可以逐渐演化，并在最终发挥其巨大的潜力。事实上，对游戏进行抽象，将核心部分抽象出来做好可扩展性花费不了多少功夫，并且能很大程度减少后续因为写死带来的麻烦。

#### 4 将控制流写成脚本
脚本只是一种在代码外定义行为的方法。脚本对于定义游戏中的步骤顺序或者需要触发的游戏事件非常有用。
脚本语言设计需要注意**分支命令**。分支有两种方法：
- 直接将变量保存在脚本语言中，并用数学运算符进行比较
- 直接调用独立存在于代码中用于比较变量的评价函数
对于上述两种方法，可以互相结合使用，但是**要确保脚本的简洁**
如果需要创建独一无二的脚本，你要需要对应的脚本语言以及用来解析脚本语言的脚本分析器，如果有必要的话，甚至还需要一个编译器来讲脚本转换成二进制文件以便快速执行。

#### 5 脚本不适用的情况
使用脚本编写数据驱动行为时使用数据驱动方法的必然后果。不过你需要记住数据驱动的核心思想：**将逻辑和数据分开，复杂的逻辑在代码中运行，数据则保留在外面**。
但是在某些情况下，在处理复杂的游戏逻辑时不得不将数据引入，导致生成十分复杂的脚本。
>为什么要把复杂逻辑保持在代码中？
>原因在于功能性和调试性方面的问题。由于·脚本不直接在代码中，它们需要重复很多编程语言中已有的概念。这种倾向的结果就是，让其拥有越来越多的功能，直到与真实的语言相媲美。但是脚本在变得复杂的同时，我们也需要更多的调试信息去找出脚本中的错误。

##### 模糊的边界
脚本的触发原则：如果逻辑太复杂，那么就应该放在代码中。脚本语言应该保持简洁，以便保证不会因为脚本的加载消耗太多的资源。
> 总之要记住，你不希望自己的游戏设计师或者脚本编写人员对游戏进行编程。有些时候，当程序员创建脚本语言时，他们会试图逃避责任。“引诱”游戏设计师对游戏编程实在是太容易了。

#### 6 避免重复的数据
绝对不要复制代码，这是一条编程实践标准。如果在两个不同的地方你需要用相同的行为，这个行为只能存在于一个地方。这种思想也可以应用于数据，可以采用因用来指向全局的数据块。甚至，通过使用引用指向数据块并修改其中的一些值，可以实现类似的继承的概念。

####  7 开发工具来生成数据
在大型游戏中，文本文件最终会变得不好控制和处理。实际的解决办法是**采用一个工具来写这个文本文件**。这种工具可以叫做游戏编辑器、关卡编辑器或者脚本编辑器。采用工具来写并不会改变数据驱动的思想，它只会使得数据驱动更加健壮、更加高效。

#### 8 结论
采用数据驱动方法很容易，但是得到显著的效果缺不容易。当所有的东西都采用数据驱动的时候，你将有无限种发展的可能。

### 1.1 面向对象的编程与设计技术
---


### 快速数据载入技巧
> Author: John Olsen
> Contact: ???

#### 预处理你的数据
在所有你能做的事情里面，**最重要的是尽可能去预处理你的关卡数据**。
为了最终获得更短的数据载入时间，需要将你的数据预处理为游戏中的最终使用的格式，只需要做这些计划，你就可以让你的C++类或者C结构的布局更适合高速载入。所有被需要被存储的数据都必须是非静态类成员变量，并且不能将指针保存到数据文件中。
> 如果你的数据里面需要用到指针，则必须确保在它们被完全载入并正确设置以后才使用，因为指针在载入后通常都指向无效数据。另外一种解决方法是将指针改为句柄或者索引。

#### 保存你的数据
无论是在游戏中还是在独立的预处理工具中，一旦数据被填充到结构中，你就可以将他们写到硬盘上，对C++来说，你可以使用`this`指针和`sizeof()`来处理类。
> 对C来说，你只需要结构指针和`sizeof()`来处理结构。需要你确保你没有使用`sizeof(this)`,否则获取的不是结构的大小而是指针的大小。

```C
#include <stdio.h>
class GameData{
public: 
	bool Save(char *fileName);
	bool Load(char *fileName);
	bool BufferedLoad(char *fileName);
	//Add accessors to get to your game data、
private:
	//一次仅打开一个文件
	static FILE *fileDescriptor;
	//Data
	int data[1000];
};
bool GameData::Save(char *fileName)
{
	fileDescriptor=fopen(fileName,"wb");
	if(fileDescriptor)
	{
		fwrite(this,sizeof(GameData),1,fileDescriptor);\
		fclose(fileDescriptor);
		return TRUE;
	}
	else
	{
	//报告文件出错
	return FALSE;
	}
}

```

#### 使用简单方法载入你的数据
使用上述方法保存数据，可使应用程序在以后需要载入关卡时，非常轻松读回它们。只需要将数据读回到游戏中和写入它们时相同的结构或类中。
```C++
bool GameData::Load(char *fileName)
{
	//以读模式打开文件
	fileDescriptor=fopen(fileName,"rb");
	if(fileDescriptor)
	{
		fread(this,sizeof(GameData),1,fileDescriptor);
		fclose(fileDescriptor);l
		//报告读取文件成功
		return TRUE;
		}
		else
		{
			//报告读取文件错误
			return  FLASE;
			}
}
```

#### 更加安全地载入你的数据
*对于某些游戏控制台硬件，至少有一件非常重要的事情需要注意。一些系统总是要读完当前硬盘扇区*。
**Memory Stomp**: 如果要直接读取数据到你的结构中，而且结构的长度不是2048字节的倍数，那你就破坏了紧接在那个结构之后的内存数据
为了避免这种**Memory Stomp**，需要有一个足够大的临时缓冲区来保存填充到2K边界的数据文件。如果需要读取多个文件，不要每次都分配和释放一个新的缓冲区。而是改用一个最大缓冲区，只分配一次，并且在所有的读取操作中重用它。完成所有操作之后再释放。
```C++
//检查你的硬件中一次读取块的大小
//将值放在这个定义中
#define READ_GREANULARITY 2048
bool GameData::BufferedLoad(char *fileName)
{
	//确认读缓冲区存在足够的空间
	//如果使用READ_GRANULARITY 的倍数，使用的空间回小一点，但是下面的会稍微快点
	char *tempBUffer= new char[sizeof(GameData)+READ_GRANULARITY];
	if(!tempBuffer)
	{
		//不能分配，返回错误代码
		return FALSE;
	}
	fileDescriptor= fopen(fileName,"rb");
	if(fileDescriptor)
	{
		fread(tempBuffer,sizeof(GameData),1,fileDescriptor)	;
		fclose(fileDescriptor);
		memcpy(this,tempBuffer)；
		delete tempBuffer;
		//报告读文件成功
		return TRUE;	
	}
	else 
	{
		delete tempBuffer;
		//报告读文件错误
		return FALSE;
	}
}
```
现在已经掌握了高度优化的关卡载入。通过预处理数据，你将节省下将数据转换为可用格式的CPU时间，并且减少了需要读取数据的总量。

### 基于帧的内存分配
> Author: Steven Ranck

本文主要介绍了一个简单而且极快速的内存分配系统，以防止在游戏关卡中出现内存碎片。

#### 常规内存分配的挑战
包括`malloc()`和`new`在内的标准内存分配系统最大的问题在于，**内存会变为碎片，从而导致游戏的性能下降，并且可能会导致无法分配一个可用的大内存块**。当然可以使用系统所提供的高级内存管理系统，但是这么做会浪费很多的性能，因为操作系统所提供的内存管理系统并不是十分地高效。并且在一些特殊情况下，没有成熟的内存管理器可以使用。
#### 基于帧的内存
解决办法：*采用基于帧1的内存*。基于帧的内存可以消除内存碎片的问题并且使用起来十分快速。
缺点：采用帧的内存不能用来作为类似`malloc()`和`new`的基于一般目的的内存分配系统。
采用帧的内存最适合用于游戏和关卡初始化模块。

![[Pasted image 20250602151903.png]]

```C++
typedef unsigned char u8;
typedef unsigned int uint;
#define ALIGNUP(nAddress, nBytes)((((uint)nAddress)+(nBytes)-1)&(~((nBytes)-1)))
static int _nByteAlignment;//内存对齐的字节数
static u8 *_pMemoryBlock;//malloc()
static u8 *_apBaseAndCap[2]; //[0]=基指针，[1]=顶指针
static u8 *_apFrame[2]; //[0]=低帧指针，[1]=高帧指针
//必须在游戏初始化时调用一次且最多一次
//nByteAlignment 必须是2的幂
//成功返回0，失败返回-1
int InitFrameMemorySystem(int nSizeInBytes,int nByteAlignment){
	nSizeInBytesALIGNUP(nSizeInBytes,nByteAlignment);
	//首先分配我们的内存块
	_pMemoryBlock = (u8*)malloc (nSizeInBytes+nByteAlignment);
	if(_pMemoryBlock == 0){
		//没有足够内存，返回错误标志
		return 1;
	}
	_nByteAlignment =nByteAlignment;
	//设置基指针
	_apBaseAndCap[0]=(u8*)ALIGNUP(_pMemoryBlock,nByteAlignment);
	_apBaseAndCap[1]=(u8*) ALIGNUP(_pMemoryBlock+nSizeInBytes,nByteALignment);
	//最后，初始化低帧指针和高帧指针
	_apFrame[0]=_apBaseAndCap[0]
	_apFrame[1]=_apBaseAndCap[1]
	return 0;
}
void ShutdownFrameMemorySystem(void){
	free(_pMemoryBlock);
}
```
函数`IntFrameMemorySystem()`在游戏初始化时会被调用一次且最多一次
传入参数：**帧内存系统管理最大内存数、字节对齐数**
**所有通过帧内存系统进行分配操作都必须保证内存对齐**[^1]

#### 分配和释放内存
分配帧内存的工作类似于堆栈操作。一个系统调用从一个或者两个堆中请求一块内存。如果制定了低堆，低堆帧指针将根据分配空间大小而增长，改变的值被返回。低堆指针总统是指向下一个可分配的内存字节。
另一方方面，如果制定了高堆，高堆指针总是指向最后分配的内存字节。如果两个帧指针彼此交错，则没有足够内存满足需求。
```C++
//返回内存块的基指针，或者在没有足够内存时返回0
//nHeapNum是堆编号：0=低，1=高
void *AllocFrameMemory(int nBytes,int nHeapNum){
	u8* pMem;
	//首先将请求内存对齐：
	nBytes=ALIGNUP（nBytes,_nByteAlignment);
	//检查可用内存
	if(_apFrame[0]+nBytes>_apFrame[1]){
		//没有足够内存
		return 0;
	}
	//现在完成内存分配操作
	if(nHeapNum){
		//从高堆向下分配
		_apFrame=[1]-=nBytes;
		pMem= _apFrame[1];
	}else
	{
		//从低堆向上分配
		pMem=_apFrame[0]'
		_apFrame[0]+=nBytes;
	}
	return (void *) pMem;
}
```
这个函数能非常快速地完成基于帧地内存分配。既然帧内存通过类似堆栈地方式分配，它也需要使用同样的方式释放。
帧是游戏内从内存系统获取用来释放内存的句柄。内存只能通过帧来释放。帧起到类似系统分配的内存页中书签的作用。
![[Pasted image 20250602162740.png]]
```C++
typedef struct{
	u8* pframe;
	int nHeapNum;
} Frame_t;
//返回一个可以在以后用于释放内存的帧句柄
//nHeapNum是堆的编号：0=低，1=高。
Frame_t GetFrame(int nHeapNum)
{
	Frame_t Frame;
	Frame.pFrame=_apFrame[nHeapNum];
	Frame.nHeapNUm= nHeapNum;
	return Frame;
}
```
对内存系统来说，一个帧数只是一个堆编号和当前堆的帧指针的拷贝。但是对于游戏，它只是一个简单的句柄，`Frame_t`。要释放内存，我们实现了下列的函数
```C++
void ReleaseFFrame(Frame_t Frame){
_apFrame[Frame.nHeapNum]=Frame.pFrame]
}
```
游戏调用`ReleaseFrame()`函数来释放从调用`GetFrame()`函数来获取帧以来分配的所有内存。

#### 结论
既然基于帧的内存以类似堆栈的方式操作，它就需要帧被以获取顺序相反的顺序释放：**否则内存可能被破坏**。检测释放违反这个条件的方法很简单，可以使用以下函数替换释放帧的函数
```C++
void ReleaseFrame(Frame_t Frame0){
//检测低堆中释放操作的有效性
assert(Frame.nHeapNUm==1 || (uint)Frame.pFrame<=(uint)_apFrame[0]);
assert(Frame.nHeapNUm==0 || (uint)Frame.pFrame<=(uint)_apFrame[1]);
//释放帧
_apFrame[Frame.nHeapNum]=Frame.pFrame;
}
```
这段代码可以在游戏的调试版本中检测出以不正确的顺序释放帧的企图。还饿可以加入更多的断言以检测其他参数的有效性问题。
最后需要说的是，对于使用多种无关的内存类型(主内、声音、贴图、图形等)的游戏平台，对于每一种内存类型，我们可以很容易地用一个基于帧内存系统来实现，然后使用主帧将他们联系到一
起。


### 简单快速的位数组
> Author:Andrew Kirmse

位数组的优势：*速度快，并且能高效组织数据*
位数组的缺点：容易出错且根机器字长相关
**我们需要的是一种抽象的位操作，能够带给我们位操作的优点，并且可以避免底层的细节**
#### 概述
本文通过三个C++类来实现了位数组。
基类`BitArray`是一个简单的一维位数组
子类`BitArray2D`是一个二维位数组
子类`TwoBitArray`是一个值为0~3的整数数组

> C++STL在位集头文件中包含一个一维位数组。尽管具有丰富的特性，但是大多数实现被隐藏了，并且难以扩充或者修改

#### 位数组
基类`BitArray`使用起来跟普通C++中的bool类型数组很类似，由一堆0和1组合起来的整数。这些位存储在字节序无关的`long`类型缓冲区中。语法上你可以将`BitArray`以与标准C++数组相同的方式使用，但它还同时具有动态数组的优点和附加的操作符。
为了保证高性能，`BitArray`在创建时不做任何初始化工作。`Clear()`方法设置所有位为`false`。
作为优化特性之一，小的`BitArray`可以存储在一个机器字中而无需分配额外的内存。使得这类同样适用于很小的标志集合。


#### 其他数组
`BitArray2D`类似一个二维的`BitArray`。它的大多数行为跟其他的二维数组一样。
```C
BitArray2D bits(10,20);
bit[5][4]=true;
```
> 注意，这里的`BitArray2D`数组下表运算符返回一个`ArrayProxy`，这是一个代理类，表示**数组中的单独一行**这个机制被用来模拟C++中双重下标的语法

`BitArray`
```
bits2d[5]=bit_array;  //illegal
bits2d[5].FlipAllBits(); //illiegal
```
虽然这样操作没什么大问题，但是会严重增加`ArrayProxy`的复杂度
实际上`BitArray2D`实现上就是一个包含二维数组所需位数的独立`BitArray`类。但是因为`BitArray2D`不提供相同的公共接口，所以它私有集成`BitArray`类。类似`BitArray`，其所有函数都短小并且内联。
最后一个类，`TwoBitArray`，提供一个二位值数组。它的实现也是简单地使用包含两倍于`TwoBitArray`元素数量的`BitArray`。


### 在线游戏的网络协议

### 最大限度地利用Assert
> Author: Steve Rabin

本篇如何在对大限度的情况下使用断言

#### Assert 基础
每个程序员都应该能够"虔诚"地使用Assert宏。Assert宏是一个简单的、无需额外代价的、针对你的假设进行双重检测的工具，它时时刻刻都在保护着你。
向量归一化是一个非常需要使用断言的例子。
```C
void VectorNormalize(Vec* src, Vec* dst)
{
	float length;
	assert(src != 0); //检查Src向量不为空
	assert(dst != 0); //检查dst向量不为空
	length =sqrt ((src-> x*src->x)+(src->y*src->y)+(src->z*src->z));
	assert(length !=0); //检查length不为0(避免被零除异常)
	dst->x=src->x/length;
	dst->y=src->y/length;
	dst->z=src->z/length;
}
```

#### Assert技巧#2：嵌入更多的信息
有时候程序员会在程序运行到一个无法料到的位置时简单地输入`assert(0)`。你可以使用相同的技巧，通过简单的取反字符串导致失败来插入描述字符串。以便提供更多的调试信息。
#### Assert技巧#3: 使之更好用一些
前面两个技巧可以通过写一个简单的宏合并到一起。这个宏接受一个参数，第一个参数是需要计算的条件；第二个则是描述字符串。它模拟了前两个技巧，以便于输入和阅读：
```C
#define Assert(a,b) assert(a && b)
//下面两个是新的宏
assert(src != 0,"VectorNormalize:src vector pointer is NULL");
assert(0,"VectorNormalize: The code should never get here");
```
#### Assert 技巧#4：编写自己的assert宏
最终，所有人都会使用被真正定制后assert宏。通过编写自己的断言对话框代码可以获得更多的控制并可新增特性。
标准C语言有一个令人十分讨厌的问题：**它会在调试器里中断代码到`assert.c`文件，而不是让你程序中断言出现的行。通过编写自己的`assert`宏，调试器可以直接中断到输入断言的行。这就避免了位找到你实际感兴趣的代码而所做的毫无意义的堆栈跟踪**。
```C
#if defined(_DEBUG)
extern bool CustomAssertFunction(bool,char *,int ,char *);
#define Assert(exp,description)
	if (CustomAssertFunction((int)(exp),description,__LINE__,__FILE__))
	{_asm{int 3}}
	#else
	#define Assert(exp,description)
	#endif
```
上面宏调用`CustomAssertFunction`函数
> `CustonAssertFunction`函数应该弹出一个显示断言信息的对话框，允许使用者继续或者中断程序的运行。如果使用者选择中断，`CustomAssertFunction`函数应该返回TRUE,调试器应该中断到断言所在行。否则应该返回FALSE，继续运行

#### Assert技巧#5：无价之宝
一旦有一个自定义的assert宏，你就可以为你的断言对话框增加一个“总是忽略”的选项。它允许你在忽略一个断言后从此不再提示此断言。
要实现这个特性，每个断言都必须跟踪自己是否被忽略的状态，并且禁止自己在此后被激发。
以下是一个简单的实现方式：
```C
#if defined(_DEBUG)\
extern bool CustomAssertFunction(bool, char *, int,char *, bool *);
#define Assert(exp,description) {static bool ignoreAlways=falsel;\
	if (CustomAssertFunction((int)(exp),description,
		__LINE__,__FILE__,&ignoreAlways)))
		{_asm{int 3}}
		}
}
#else
#define Assert(exp,description)
#endif
```
#### Assert技巧#6:给“超级铁杆”
对于断言提供了大量重要调试信息，但是并不是所有的错误来源都在断言函数内，真正的错误只会在调用`VectorNormalize`函数的函数中发生。
如果断言失败时没有运行调试器，断言实际上并没有起到任何的作用。
一个简单的解决方法是在断言对话框中提供堆栈的信息。

#### Assert技巧#7： 保持简单——复制粘贴断言信息
确保你给出的断言的信息时能够正确的被复制出来，并且能够被正确解读。
```C
if(OpenClipCard(NULL))
{
	HGLOBAL hMem;
	char szAssert[256];
	char *pMem;
	
	sprintf(szAssert,"Put assert into here");
	hMem=GlobalAlloc(GHND|GMEM_DDESHARE,strlen(szAssert)+1);
	if(hMem){
	pMem=(char*)GlobalLock(hMem);
	EmptyCLipboard();
	SetClipboardData(CF_TEXT,hMem);
	
	}
	CloseClipboard();
}
```

### 实时的游戏内建剖析
剖析代码是在大部分软件开发中一个十分常见的流程步骤，这一步虽然是常规流程但是十分重要——因为对于游戏来说性能优化指标是一个十分重要的指标，本文主要展示了如何对代码进行剖析，并且如何获取到剖析得结果。
#### 开始考虑细节
这个实时剖析器允许你监控你感兴趣的任意代码点或者片段。你只需要在希望剖析的区域开始和结束的时候调用一个对应的函数就行。
每个样本中都包含以下的东西：
- 一个`ProfileBegin`
- 一个`ProfileEnd`

###### 剖析的代价
从整体上来说，剖析器用来保持对你的样本跟踪时间总量可以忽略不计，特别是当你每次只需要检测少量的几个点时。

#### 剖析器应该告诉你的东西
一个合格的剖析器应该告诉调试者以下的信息：
- 样本点的唯一名称
- 在此样本上耗费的平均、最小和最大帧时间比例
- 每帧中此样本被调用的次数
- 此样本点与其他样本点的关系
剖析器视图智能处理样本并保持对其父子关系的跟踪。
#### 剖析器的实现
对于一个给定帧中，每个剖析的样本需要下列信息：
```C
typedef struct{
	bool bValid;    //检测数据是否有效
	uint iProfileInstances; //ProfileBegin调用次数
	int iOpenProfiles; //没有相匹配的ProfileEnd调用的ProfileBegin调用次数
		char szName[256];//样本名称
		float fStartTime; //当前样本开始时间
		float fAccumulator; //帧内所有样本统计
		float fChildrenSampleTime; //所有子样本耗时
		uint iNumParents; //父样本数
} ProfileSample;
```
我们需要跨越多帧保持样本历史信息，将在下列结果中被存储：
```c
typedef struct{
	bool bValid; //数据是否有效
	char szName[256]; //样本名称
	float fAve; //每帧平均时间(百分比)
	float fMin; //每帧最小时间(百分比)
	float fMax; //每帧最大时间(百分比)
} ProfileSampleHistory;
```
为了简单和速度考虑，将预分配`ProfileSample(s)`和`ProfileSampleHistory(s)`数组。
> 预分配数组可以使得不需要去管理有关的内存。在任何样本被采样之前，调用`ProfileInit`来初始化两个数组并记录开始时间。

两个函数会被用来获取时间：
- `GetTime`:以秒的形式返回系统时间
- `GeiElapsedTime`：返回上一帧完成以来耗费的所有时间(1/current_frame_time)
```C
#define NUM_PROFILE_SAMPLES 50
ProfileSample g_sample [NUM_PROFILE_SAMPLES];
ProfileSampleHistory g_history[NUM_PROFILE_SAMPLES];
float g_startProfile = 0.0f;
float gendProfile = 0.0f;
void ProfileInit(void)
{
	uint i;
	for(i=0;i<NUM_PROFILE_SAMPLES;i++){
		g_samples[i].bValid =false;
		g_history[i].bValid = false;
	}
	g_startProfile = GetTime();
}
```

#### ProfileBegin的细节
我们只需要关注几个变量和函数：
- ProfileBegin函数：此函数被调用时，它会首先检查是否已经有一个同名的样本存在。如果找得到，那么说明此样本在此帧之前就已经被调用过。
- iOpenProfiles：被ProfileBegin函数递增，并被ProfileEnd递减。*事实上它被用于跟踪有多少样本开始但没有结束。*
> 注意这个实现完全不处理递归调用(一个样本在结束前开始多次)
- iProfileInstance：被ProfileBegin递增以统计样本在帧中被调用的次数。

#### ProfileEnd的细节
实际上大部分的工作都是在ProfileEnd中完成的。ProfileEnd函数计算结果并评估父子关系
具体细节：
1. ProfileEnd在数组中先找到样本。一旦找到样本，结束时间将被记录下来并会递减iOpenProfiles
2. 代码循环遍历所有样本，统计有多少样本的剖析没有结束(父亲)，并将最近的一个为结束的样本的索引记录下来，
3. 父节点的数量将被记录到iNumParents。如果有父节点，则样本时间将被在直接父节点的结构中

#### 处理剖析数据的细节
所有剩下的工作将是处理、格式化并将数据导出到文本缓冲区。这些工作在游戏主循环的最后由ProfileDumpOutputToBuffer函数完成。
> 注意两个函数ClearTextBuffer和PutTextBuffer被用于将文本到输出缓冲区。你必须提供这些函数。


#### 后期增强

#### 将它们组合起来


## 数学技巧

### 三角函数的多项式逼近
本文讲了如何使用算法去快速逼近，从多项式出发到级数，最后到三角函数的级数展开的快速逼近
在访问一个单独的存储单元的时间内，我们通常能完成大量的计算工作，而且相比任何接近同一程度的算法，这些计算免受量化误差之苦。
多项式逼近，寻找可以逼近我们想要的函数的一个多项式，然后将$x$的值代入这个多项式并得到一个该函数的近似值。
计算函数的问题可以转化为寻找一个好的多项式以用于逼近的问题了。这其实是对能给出好结果的“魔术"数——多项式系数的寻找。
#### 多项式
多项式就是关于变量(x)的幂次项的和，每一个幂被一个系数相乘。多项式的标准写法如下所示：
$$
a[0]+a[1]\times x+a[2] \times x^2 +a[3] \times x^3+\cdots +a[d] \times x^d
$$
数$a[i]$被称为多项式的系数，数字$d$是多项式的次数。
C++语言的表示形式如下所示：
```C++
float PolyL::Evaluate(float x)
{
	float powx = 1;
	float sum = 0;
	for (int n = 0; n<=d; n++)
	{
	sum+=a[n] *pow[x];
	powx *=x;
	}
	return sum;
}
```
这是最显然和最直接的应用程序来计算多项式的方法，除此之外还有许多替代方法。**但是这些替代方法在某种方面依赖于多项式的因式分解，因此不推荐替代方法**
采用因式分解不推荐的原因：
- 分解因式常常导致对每个系数做除法，这意味着系数对舍入误差是敏感的(尤其是在单精度浮点数中)
- 用因式分解计算有一个包含所有乘法的关键路径，而用简单的方法计算有一个只包含一个乘法和一个加法的关键路径。故简单方法在CPU上流水线处理更好，这是很重要的。

#### 定义域和值域
一个函数的定义域式它能够被调用的范围。值域是在它的定义域上的返回值的范围。
近似多项式以下形式的项的和：
$$
a[n] \times pow(x,n)
$$
这些项中的每一项本身必须是一个浮点数，所以一定在一个浮点数的范围内，即大约在$0到$$2^{127}$
如果我们取该多项式的2基对数，得到:
$$
log2(a[n] \times pow(x,n))=log 2(a[n])+n\times log 2(x)<127
$$
它给出了一系列的$log2(x)$必须服从不等式。
 

[^1]:注意，`ALIGNUP`宏需要`nBytes`参数值为2的幂

