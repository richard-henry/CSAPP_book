# <center> Chapter 5 Optimizing Program Performance

## Generally Useful Optimizations

### Code Motion
<font size =4.5 face="Consolas">

* Reduce frequency with which computation performed 
  * if it will always produce same result
  * Especially moving code out of loop
<b>
```c
//before optimization
long j;
for(j=0;j<n;j++)
a[n*i+j]=b[j];
```

```c
//after optimization  (take some step out of loop)
long j;
int ni = n*i;
for(j=0;j<n;j++)
a[ni+j]=b[j];
```
</b>
</font>

### Reduction in Strength
<font size =4.5 face="Consolas">

* Replace costly operation with simpler one
* Shift, add instead of multiply or divide
</br>

    $16 * x $   --> $x << 4$
  * Utility machine dependent
  * Depends in cost of multiply or divide instruction
        -On Intel Nehalem, integer multiply requires 3 CPU cycles
* Recognize sequence of products
<b>
```c
//before optimization
for(i=0;i<n;i++){
    int ni=n*i;
    for(j=0;j<n;j++)
        a[ni+j]=b[j];
}
```

```c
//after optimization  (multiplication -> addition)
int ni=0;
for(i=0;i<n;i++){
    for(j=0;j<n;j++)
        a[ni+j]=b[j];
        ni+=n;
}
```
</b>
</font>

### Share Common Subexpressions 
<font size =4.5 face="Consolas">

* Reuse portions of expressions
* GCC will do this with -O1
<b>  
```c
up = val[(i-1)*n + j];
down = val[(i+1)*n + j];
left = val[i*n+j-1];
right = val[i*n+j+1];
sum = up + down + left + right;
```
```c
long inj = i*n + j;
up = val[inj - n];
down = val[inj +n];
left = val[inj -1];
right = val[inj +1];
sum = up + down + left + right;
```
</b>
</font>

## Optimization Blocker

### Procedure Calls
<b>
<font size =4>

```c
void lower(char *s)
{
    size_t i;
    for(i=0; i< strlen(s); i++)     //We call strlen() in every loop 
        if(s[i]>='A' && s[i] <= 'Z')
          s[i] -= ('A'-'a');
}
```
</b>
<font size =4.5 face="Consolas">

***Why couldn't compiler move*** **strlen()** ***out of inner loop?***

* Procedure may have side effects
  * Alters global state each time called
* Function may not return same value for given arguments
  * Depend on other parts of global state
  * Procedure **lower** could interact with **strlen**
<font color ='red'>

<b>Warning:</b>  </font>
* Compiler treats procedure call as a black bos
* Weak optimization near them 
  
**Remedies:**
* Use of inline functions
  * GCC does this with -O1
        - Within single file
* Do your own code motion
</font>

### Memory Matters
<b>
<font size =4.5>

```c
void sum_rows1(double *a, double *b, long n){
    long i, j;
    for(i=0; i<n; i++){
        b[i]=0;
        for(j=0; j<n; j++)
            b[i] +=a[i*n + j]
    }
}
```
</b>
<font size =4.5 face="Consolas">

* Code updates b[i] on every iteration
* Must consider **Memory aliasing** (**b** might point to some parts of array **a**)

* Too many times of reading and writing data between memory and register files, actually there 's **no need to store intermediate results** 

<b>

```c
void sum_rows2(double *a, double *b, long n){
    long i,j;
    for(i=0; i<n;i++){
        val+=a[i*n+j];
    b[i]=val;        
    }
}
```
</b>
</font>

## Exploiting Instruction-Level Parallelism

<font size =4.5 face="Consolas">

**Need general understanding of modern processor design**
* Hardware can execute multiple instructions in parallel

**Performance limited by data dependencies**

**Simple transformations can yield dramatic performance improvement**
* Compilers often cannot make these transformations
* Lack of associativity and distributivity in floating arithmetic
</font>

### Benchmark Computation 
<b>

```c 
void combine1(vec_ptr v, data_t *dest)
{
    long i;
    *dest = IDENT;
    for(i = 0; i < vec_length(v); i++){
        data_t val;
        get_vec_element(v, i, &val);
        *dest = *dest OP val;
    }
}
```
</b>
<font size =4.5 face="Consolas">

**Data Types**
Use different declarations for *data_t*:
* int 
* long 
* float
* double
  
**Operations**
Use different definitions of *OP* and *IDENT*
*  \+ 0
*  \* 1
</font>

### Cycles Per Element(CPE)
<b>

* Convenient way to express performance of program that operates on vectors or lists
* Length = n
* In our cases:<font color ='red'>CPE=cycles per OP </font>
* T = CPE * n + Overhead
  * </b>CPE is slope if line
</font>

### Benchmark Performance
<b><font size =4.5 face="Consolas">

|<font color ='red'>Method|<font color ='red'>Integer|| <font color ='red'>Double FP||
|-|-|-|-|-|-|
|Operation|Add|Mult|Add|Mult|
|Combine1 <br/> unoptimized|22.68|20.02|19.98|20.18|
|Combine1 -O1|10.12|10.12|10.17|11.14|
|Combine4<br/>Move functions<br/>outside|1.27|3.01|3.01|5.01|
|Latency Bound|1.00|3.00|3.00|5.00|

```c
void combine4(vec_ptr v, data_t *dest)
{
    long i;
    long length = vec_length(v);
    data_t *d = get_vec_start(v);
    data_t t = IDENT;
    for(i = 0; i < length; i++)
        t = t OP d[i];
    *dest = t;
}
```

* Move vec_length out of loop
* Avoid bounds check each cycle
* Accumulate in temporary
* Doing  <font color='red'> serial </font>Operations (Which don't make use of pipeline)
</b>
</font>

### Loop Unrolling   (2$\times$1)
<b>
<font size =4.5>

```c
void unroll2a_combine(vec_ptr v, data_t *dest)
{
    long length = vec_length(v);
    long limit = length -1;
    data_t *d = get_vec_start(v);
    data_t x =IDENT;
    long i;
    //Combine 2 elements at a time
    for (i = 0; i < limit; i+=2){
        x = (x OP d[i]) OP d[i+1];
    }
    for(;i < length; i++){
        x = x OP d[i];
    }
    *dest = x;
}
```
</b>

<b><font size =4.5 face="Consolas">

|<font color ='red'>Method|<font color ='red'>Integer|| <font color ='red'>Double FP||
|-|-|-|-|-|-|
|Operation|Add|Mult|Add|Mult|
|Combine4|1.27|3.01|3.01|5.01|
|Unroll 2$\times$1|1.01|3.01|3.01|5.01|
|Latency Bound|1.00|3.00|3.00|5.00|

</b>

* **Helps integer add**
   * reduce the times of incrementing for loop
* **Others don't improve**
   * Still <font color ='red'>sequential </font>dependency

#### Unrolling 2$\times$1a
<b>

```c
x = x OP (d[i] OP d[i+1]);
```
* Breaks sequential dependency
</b>

<b><font size =4.5 face="Consolas">

|<font color ='red'>Method|<font color ='red'>Integer|| <font color ='red'>Double FP||
|-|-|-|-|-|-|
|Operation|Add|Mult|Add|Mult|
|Combine4|1.27|3.01|3.01|5.01|
|Unroll 2$\times$1|1.01|3.01|3.01|5.01|
|Unroll 2$\times$1a|1.01|1.51|1.51|2.51|
|Latency Bound|1.00|3.00|3.00|5.00|
|Throughput Bound|0.50|1.00|1.00|0.50|

</font>
</b>

### Branch Prediction 
