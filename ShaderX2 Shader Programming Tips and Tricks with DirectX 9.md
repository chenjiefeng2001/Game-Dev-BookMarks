> Author: Wolfgang F.Engel
## 第一部分 几何变换技巧
> 本部分主要介绍有关顶点的几篇文章
> 1. Using Vertex SHaders For Geometry Compression
> 2. Using Lookup Tables in Vertex Shaders
> 3. Terrian Geomorphing in the Vertex Shader
> 4. 3D Planets on the GPU
> 5. Cloth Animation with Pixel and Vertex Shader 3.0
> 6. Collision Shaders
> 7. DCisplacement Mappling

### Using Vertex SHaders For Geometry Compression/使用顶点着色器进行几何压缩
> 本篇文章是之前的文章——*《Dirct3D ShaderX: Vertex and Pixel Shader Tips and Tricks》*
> 的续集

#### DirectX 9新特性
##### 顶点着色器更新
由原来的1.1版本更新为3.0，提供了一种新的功能，最突出的是顶点纹理。这将提供一系列的新的压缩方法，但由于当前缺乏硬件支持，并未进行探索(已解决)

##### 新的顶点流声明格式
DX9对于DX8来说期顶点流声明系统被完全重写，让其使用更加简便并且增加了新的功能。从压缩的角度来看，最有趣的项目是**新的顶点数据类型以及额外控制每个元素在流中的来源(*流偏移*)**
###### 局限性
如果使用的是DX8驱动程序，DX9格式的顶点流声明的许多新功能都无法使用。并且如果在DX7的啊驱动程序下，还必须坚持FVF格式的风格声明。除此之外，如果声明中的流偏移产生重叠的顶点元素，即使在DX9下也必须设置D3DDEVCAO32_VERTEXELEMENTSCANSHADERTREAMOFFSET功能位。另一个局限性是流偏移必须对齐DWORD边界(4 bytes)
新的顶点数据类型现在为DX9引入的每种新类型(以及DirectX 8的UBYTE4)都提供了能力位；在使用之前必须检查这些位。如果您想要的数据类型的能力位已设置，则使用已经设置的。否则必须通过顶点着色器的代码来模拟该功能或者更改顶点数据为再次硬件上的可用形式。

##### 新的顶点数据类型
这些新的数据类型中大多数是现有的DX8数据类型的有符号、无符号和归一化版本，但也有一些增加了新功能。以下表格暗通道位数对数据类型进行排序。


|   数据类型    | 通道数 | 每位比特 | 每位比特通道 |    顶点着色器寄存器的范围     | 大写位 |   备注    |
| :-------: | :-: | :--: | :----: | :----------------: | :-: | :-----: |
| D3DCOLOR  |  4  |  32  |   8    |       [0,1]        |  N  | a  [^1] |
|  UBYTE4   |  4  |  32  |   8    |      [0,255]       |  Y  |         |
|  UBYTE4N  |  4  |  32  |   8    |       [0,1]        |  Y  |         |
|   UDEC3   |  3  |  32  |   10   |      [0,1024]      |  Y  | b [^2]  |
|   DEC3N   |  3  |  32  |   10   |       [-1,1]       |  Y  |    b    |
|  SHORT2   |  2  |  32  |   16   |   [-32768,32767]   |  N  |         |
|  SHORT4   |  4  |  64  |   16   |   [-32768,32767]   |  N  |         |
| USHORT2N  |  2  |  32  |   16   |       [0,1]        |  Y  |         |
| USHORT4N  |  4  |  64  |   16   |        [01]        |  Y  |         |
|  SHORT2N  |  2  |  32  |   1    |       [-1,1]       |  Y  |         |
|  SHORT4N  |  4  |  64  |   16   |       [-1,1]       |  Y  |         |
| FLOAT16_2 |  2  |  32  |   16   |  [-6.55e4,6.55e4]  |  Y  |  c[^3]  |
| FLOAT16_4 |  4  |  64  |   16   |  [-6.55e4,6.55e4]  |  Y  |    c    |
|  FLOLAT1  |  1  |  32  |   32   | [-3.48e38,3.48e38] |  N  |  d[^4]  |
|  FLOLAT2  |  2  |  64  |   32   | [-3.48e38,3.48e38] |  N  |    d    |
|  FLOLAT3  |  3  |  96  |   32   | [-3.48e38,3.48e38] |  N  |    d    |
|  FLOLAT4  |  4  | 128  |   32   | [-3.48e38,3.48e38] |  N  |    d    |



[^1]: a) D3DCOLOR 进入顶点着色器时会重新排列元素。ARGB变为RGBA
[^2]: b) 两个最高位未被使用，如果没有显式地声明顶点流编程，那么这两个位将丢失
[^3]: float16是OpenEXR标准，由NVIDIA和PIXAR共同创建的新标准。使用D3DXFLOAT16进行操作(或openEXR SDK中的库)
[^4]: float是IEEEE754标准， 对应于C类型的float


这是一个非常丰富的数据类型集，所有数据类型都是32位的倍数(**这正是DEC3格式丢失两个位的原因**)。需要检查的保留位是在D3DCAPS9.DeclType下，具体的位是D3DTCAPS_datatype，要使用的类型是D3DDECLTYPE_datatype（其中数据类型来自上面的列表）

##### 如何恢复两个比特位
当使用DEC3N或者UDEC3格式时，我们似乎失去了两个比特位，然而实际上这两个比特位也是可以得到高效利用。通过两个不同的顶点元素指向顶点缓冲区的同一内存，我们可以访问我们的两个比特位。

如果我们将法线（一种常见的方法）存储为UDEC3并且希望回复我们的两个比特位，则单个流顶点流声明的示例如下。现在顶点着色器可以绑定NORMAL0以按UDEC3访问数据，并且将NORMAL1绑定为UBYTE4

```hlsl
D3DVERTEXELEMENT9 decl[]=
{
	//first element, a 'normal' UDEC3 declaration
		0, // streeam number 
		0, // stream offset in bytes
		D3DDECLTYPE_UDEC3, //vertex type for this access
		D3DDECLMETHOD_DEFAULT, //not used so leave at default
		D3DDECLUSAGE_NORMAL, //usage (used to bind in the vertex shader)
		0  //usage number (you can have n normals)
},
//second element, a UBYTE4 that access the same memory as the normal above
{
	0,    //stream number, same as first element
	0,    //stream offset, same as first element
	D3DDECLTYPE_UBYTE4, //vertex type for this access
	D3DDECLMETHOD_DEFAULT, //not used so leave at default
	D3DDECLUSAGE_NORMAL, //usage(used to bind in the vertex shader)
	1 //usage no 
},
D3DDECL_END()
```

要将我们的两个位转换为可以使用的形式，那么我们需要除以2的6次方(64)，然后取整。这样就会将多余的数据转移到小数点的右侧，仅仅保留整数部分，然后就可以将两个位回收，其范围在0-3之间。

```hlsl
struct VS_INPUT{
float4 normal： NORMAL0,
float4 enc2bit :NRORMAL,
};
void main(VS_INPUT input){
	//access normal as usual
	float3 normal = input.normal;
	//decode our 2 bits 
	float two_bits =floor(input.enc2Bit.w/64.0);
}
```

##### 更好的压缩转换数据类型
新的DEC3N数据类型使可以轻松设计一个具有三个通道的格式，这三个通道的精度分别为10、10和12位。这对于压缩变化后的位置很有用。[^5] 对于需要表示的物体来说，通过在主轴之外加上两个次轴，就可以使用32位的格式来表示原本需要64位才能表示的物体。


[^5]: 压缩变换在*Direct3D ShaderX 中的顶点解压缩*一文中有所讨论；简而言之，它通过求解网格位置的协变矩阵的特征值问题，将位置变换到这个基之前进行量化。在顶点着色器中解压缩矩阵向量可以恢复原始位置

```hlsl
struct VS_INPUT
{
	float4 position: POSITION0,
	float4 enc28bit: POSITION1,
};
void main(VS_INPUT input){
	//get the 110,10,10 portion of the position
	float3 cpos =input.position;
	//decode our 2 bits (0-3)
	float two_bits = floor(input.enc2Bit.w/64.0)
	//factor in the extra bits and convert back into the 0-1 range
	cpos.z=(cpos.z+two_bits)*0.25
	//transform by the inverse composition martix 
	float4 pos = mul(float4(cpos,1),InvCompressionTransform);
}
```
#### 位移压缩
之前介绍了如何使用顶点着色器来渲染置换贴图。此功能可以扩展到一种非常强大的技术，可以称之为**位移压缩**。它为一整套技术，*包括任何支持项点着色器的硬件都可以完成面片渲染、置换贴图和细分表面，并且是一种强大的集合压缩形式*。
通常，细分级别由CPU决定，因为我们目前没有可编程的细分硬件，但是有一些你可以使用的固定功能硬件细分系统，这是该技术的重大限制——在有限程度上，我们可以通过发送要裁剪的顶点来移除三角形，但是我们不可以添加三角形。
通过将顶点着色器用作函数评估器，并且通过顶点流传入参数，我们可以渲染许多几何表面。对于对于这里使用的表面，这包括一个带有附加位移标量的重心表面参数，但其他表面的参数化也是可能的。
因此需要两个组件来实现位移压缩：
- **位移贴图**：一种沿表面法线检索标量位移的方法。没有它，你的位移压缩就变成了标准表面贴片评论。
- **表面基**：每个位移压缩着色器都需要一个基系统来定义位移前的基表面。最简单的是平面，但也可以像细分表面一样复杂。
###### 位移贴图
将位移值传入顶点着色器的方法至少有四种。更高级的方法需要显式的硬件支持。(这里不做介绍，详情参考Mike Doggett 和Tom Fosyth的演示文稿)
这里介绍的技术适用于任何顶点着色器硬件，它将位移贴图视为1D的顶点流。这是对Direct3D ShaderX 中介绍的技术的一种推广，该技术隐含了一个平面基，经过一些小的修改后适用于任何表面基。
位移值显式地存储在顶点流中。如果将其保存在单独的流中，可以通过CPU作为标准位移贴图访问，或者可以选择将其与其他顶点元素打包。打包通常会节省空间，但单独的流可能更方便，尤其是对于动态更新的位移贴图。
由于只有一个通道的顶点数据类型(`FLOAT1`)，你可能会将位移贴图存储在具有额外通道的其他数据类型中对于 8 位位移图数据，UBYTE4 是明显的选择。这可能会看起来浪费很多空间，但在实践中，必须提供足够的其他数据，以便如果空间是问题，可以回收空间来存储其他表面参数。

>注意：不幸的是，**DirectX 9 没有通过 GPU 进行数据传输或共享渲染目标与顶点流的方法**。这是一个纯 API 问题，但这使得在 DirectX 9 下基于 GPU 的动态位移图变得困难（如果不是不可能的话）。Mike Doggett 的 OpenGL uber-buffer 渲染到顶点数组演示展示了 GPU 修改顶点数据可以做什么。

##### 预过滤位移贴图
使用顶点流位移时可以采用的一种过滤方式是**将较低细分级别上发生的位移值与常规位移值一起存储**。这与 Mip 映射类似，因为过滤是在实际渲染之前运行的。与 Mip 映射一样，您可以使用*点采样（仅选择适当的位移值）或线性过滤（选择两个位移值并进行线性插值）*。与 Mip 映射的主要区别在于在顶点着色器中无法轻松访问纹理导数，因此您可能需要一个全局混合因子或基于与摄像机的距离。
如果将位移值存储在UBYTE4中，可以将三个较低级别存储在其他三个通道内，这将提供一个有效的线性Mip过滤(但是具有点最小/最大过滤)
#### 表面基
位移压缩的关键在于反转顶点流和常量寄存器之间的标准关系。用于索引三角形的顶点着色器