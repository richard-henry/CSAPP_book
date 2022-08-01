# <center>Chapter1: A Tour of Computer System

#####new words: 
plague: to cause pain, suffering, or trouble to someone
confound: to confuse and surprise people by being unexpected
terminate:end
chunk: a large thick piece of something
ratify: to make a written agreement official by signing it.
overhead: 流水
incur:招致
perplexing：confusing
conduits: 导线
specify：指定，列举
contiguous：next to something or each other
interleave:夹，插入，交替
advent: the time when something first begins to be widely used
whirlwind: 旋风
razor: 剃刀
prominent: important
## Trace the lifetime of  ***hello*** program:

<font size =3 face="Consolas">

```c
#include<stdio.h>

int main(){
    printf("hello world\n");
    return 0;
}
```
**1.Information is bits + context**

**2.programs are translated by other programs into different forms**

programmer->text editor->hello.c(source program:text)
hello.c->preprocessor->hello.i(modified source program:text)
hello.i->compiler->hello.s(assembly program:text)
hello.s->assembler->hello.o(relocatable object programs: binary)
hello.o+printf.o->linker->hello(executable object program: binary)

**3.<l>It pays to understand how compilation systems work**

optimizing program performance
understanding link-time error
avoiding security holes

**4.Processors read and interpret instructions stored in memory**

hardware organization of a system 
buses
I/O Devices
main memory
processor(CPU)

**5.Caches matter**

To deal with processor-memory gap(moving information from one place to another)
Temporary staging areas for information

**6.Storage devices form a hierarchy**

**7.The operating system manages the hardware**
1 process
2 threads
3 virtual memory 
  program code and data 
  heap
  shared libraries: C standard library, math library, etc
  stack: function calls
  kernel virtual memory
4 files

**8.System communicate with other systems using networks** 

**9.Important themes**
1 Amdahl's Law
$$ T_{new}=(1-\alpha)T_{old}+\alpha T_{old}/k$$
$$ S=T_{new}/T_{old}=\frac{1}{(1-\alpha) +\alpha /k}$$

2 Concurrency and Parallelism

*Concurrency:* a system with multiple, simultaneous activities.
*Parallelism:* the use of concurrency to make a system run faster.
</font>