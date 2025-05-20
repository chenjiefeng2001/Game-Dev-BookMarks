If I had a dollar every time I heard the question “Do we not have any more memory?”  
When I ask the question “How much memory are you using for subsystem X?”, very few developers know the answer. It is usually a smaller or bigger ballpark but no definite answer.  
Memory for any application is a crucial resource. No matter what type of application you are creating you will benefit from having good memory management. With some basic memory management in place you can know your memory distribution between systems, finding memory leaks much easier, simplify memory alignment requirements? These are just a few of the areas where you will benefit greatly with good memory management.

## Getting the chaos under control

The first thing you need to do, unless you have already done it, is to override the global new and delete functions. Doing this gives you a starting point for all your memory management. All calls to new and delete will be handled by your custom functions. The very first step is to just intercept the allocation request and carry it out like it normal.

```C++
void* operator new(size_t size)
{
    return malloc(size, sizeof(char));
}
void delete (void* mem)
{
    free(mem);
}
// Don?t forget the array version of new/delete
void* operator new[](size_t size)
{
    return malloc(size, sizeof(char));
}

void delete[](void* mem)
{
    free(mem);
}
```

I?m not going to go into any detail about intercepting calls to malloc or the like. All I will say is that you will do best in staying away from using alloc, malloc, calloc, realloc and free (and all other permutations that I have forgotten). In most cases it can be tricky to intercept those calls. I would say that you should only use malloc once and that is to allocate all the needed memory for your application… but more about that later.

## Custom versions of new and delete

You will quickly find that when you get an allocation request in you ?new? handler it would be very handy to know who made that allocation. From the code doing the allocation it would sometimes be very nice to be able to also specify extra information such as the alignment needed for the memory block. Aligned memory in particular is a pain to work with unless it is supported by the memory allocator. If you have a class that has members that need to be 16-byte aligned (SSE for example) it will be messy.

```C++
// Allocate memory with the needed padding to ensure that you
// can properly align it to the required boundary. Then you need
// to placement construct the new object in that memory.
char* pInstanceMem = new char[sizeof(MyClass) + 0x0F];
char* pAlignedMem = pInstanceMem & (~0x0F);
MyClass* pMyClassInst = new (pAlignedMem) MyClass();

// The allocation of the object it not terribly ugly but it is
// a pain to work with? but not as much of a pain as it is to
// delete the instance. The code below will crash or just leak.
// The destructor will be properly called but the memory address
// pointed to by pMyClassInst was never returned from a call
// to ?new?. The memory address actually returned by the call
// to 'new' used for this object is really unknown at this point.
// What to do!?!?!?
delete pMyClassInst
```
#### Wouldn?t it be much nicer if you could allocate your MyClass instance like this…

```C++
// Just allocate with an extra argument to ?new?
MyClass* pMyClassInst = new (Align(16)) MyClass();
MyClass* pMyClassInst = new (Align(16)) MyClass();

// and deletion will be straight forward? The point
er passed to
// ?delete? is the same pointer returned from the call to ?new?.
delete pMyClassInst.
```


#### This is where overloaded versions of new and delete comes to the rescue.

```C++
// We use a class to represent the alignment to avoid any code
// situations where 'new (************0x12345678************) MyClass()' and
// 'new ((void*)0x12345678) Myclass()' might cause a placement
// construction instead of construction on an aligned memory
// address.****
class Align
{
public:
    explicit Align(int value) : m_value(value)
    {
    }
    int GetValue() const
    {
       return m_value;
    }
private:
    int m_value;
};

// Overridden 'normal' new/delete
void* operator new (size_t size);
void* operator new[] (size_t size);
void operator delete( void* mem );
void operator delete[]( void* mem );

// Aligned versions of new/delete
void* operator new[] (size_t size, Align alignment);
void* operator new (size_t size, Align alignment);
void operator delete (void* mem, Align alignment);
void operator delete[] (void* mem, Align alignment);
```

## Allocate all memory up front

Now when you have ways to allocate memory and pass arbitrary parameters to the allocator you can start organizing your memory.  
If you need to ship an application that can only use 256 MB of RAM I would suggest that you allocate 256 MB of system memory up front and call that ‘retail memory’. Most of the time you need more memory during development of various systems and for this I would allocate another clump of memory… and call this memory ‘development memory’. You now have two huge buffers to use for your application and you should not allocate any more system memory. When you receive a call to ?new? you could check a variable whether to allocate the memory from the retail or the development memory pool.  
By clearly separating the two memory pools you make sure that debug-only allocations end up in the debug pool and allocations needed to ship the game are taken from the retail pool. This way it is very clear from which pool you allocate memory and it is easy to keep track of the allocated/available memory. You could even use another custom overloaded new/delete that pass in whether the allocation should be from retail or development memory.

## Divide and specialize

Now when you have your chunks of memory it might be a good idea to split it up based on the needs of your application. After all it’s terribly impractical to have only one huge buffer for an application. I am very much against dynamic allocations at run time of an application in general but some allocations have to happen and what is the best way of handling it?

A good way to organize the memory is to split the memory chunks into smaller blocks managed by different allocators using various allocation algorithms. This will not just help to be more efficient about the memory usage but will also serve as a way to clearly assign the memory to the various systems.

Not all allocations are the same. Here are a just few common allocations that came to mind.

### Persistent Allocations

Allocated once at startup/creation of a system (pools, ring buffers, arrays). It will never be deleted and therefore we don?t need any complicated algorithms to allocate persistent memory. Linear allocations work great it this scenario. All it takes for an allocator like this is a pointer to the buffer, the size of the buffer and the current offset into the buffer (allocated space).

```C++
// Simple class to handle linear allocations****
class LinearAllocator
{
public:
    LinearAllocator(char* pBuffer, int bufferSize) :
        m_pBuffer(pBuffer),
        m_buffersize(bufferSize),
        m_currentOffset(0)
    {
    }
    void* Alloc(size_t size)
    {
       void* pMemToReturn = m_pBuffer + m_currentOffset;
       m_currentOffset+= size;
       return pMemToReturn;
    }
    void Free(void* pMem)
    {
       // We can't easily free memory in this type of allocator.
       // Therefore we just ignore this... or you could assert.    }
private:
    char* m_pBuffer;
    int m_bufferSize;
    int m_currentOffset;
};
```
### Dynamic Allocations

This memory is allocated and freed at random points throughout the lifetime of the application. Sometimes you just need memory to create an instance of some object and you can’t predict when that will happen. This is when you can use a dynamic memory allocator. This allocator will have some type of algorithm to remember what memory blocks are free and which ones are allocated. It will handle consolidation of neighboring free blocks. It will however suffer from fragmentation which can greatly reduce the amount of memory available to you. There are tons of dynamic allocation algorithms out there, all with different characteristics; speed, overhead and more. Pick a simple one to start with… you can always change later.

### One-Frame Allocations

These allocations happen for example when one system creates data and another consumes it later in that same frame. It could be variable sized arrays used for rendering, animation or just debug text created by some system earlier in the frame. This allocator handles this by resetting itself at the beginning (or end) of every frame, hence freeing all memory automatically. By doing this we also avoid fragmentation of the memory. The above LinearAllocator can be used here as well with the addition of a simple **‘Reset()’** function.

[![Memory Map](http://www.swedishcoding.com/wp-content/uploads/2008/08/memory-map.jpg "memory-map")](http://www.swedishcoding.com/wp-content/uploads/2008/08/memory-map.jpg)

## Conclusion

Well… I hope this is useful for someone out there. This type of information has helped me when I have worked on my personal or professional projects.

Another thing I did not talk much about is memory tracking. I guess all I will say about that for now is… Do it! Spend the time to implement something quick and easy to help you track down memory leaks and where in your code are you using up all the memory. This is a large topic by itself and therefore I will not write about it in this post. Make use of the overloading of the new/delete operators to allow you to pass __FILE__ and __LINE__ for each allocation. Use macros or other things to make the code prettier.

Until next time…