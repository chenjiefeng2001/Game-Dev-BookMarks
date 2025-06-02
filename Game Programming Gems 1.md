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

[^1]:(注意，`ALIGNUP`宏需要`nBytes`参数值为2的幂)

