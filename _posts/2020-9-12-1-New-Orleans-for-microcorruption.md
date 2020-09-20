# No.1 New Orleans

——[microcorruption](https://microcorruption.com/) 闯关第一关

---



## 关于**MSP430**的一些介绍

### 寄存器

MSP430有16个寄存器，分别包括：程序计数器(computer counter) ```R0```，栈指向寄存器(stack pointer) ```R1```，状态寄存器（status register)```R2```，常数产生寄存器(constant generation)```R3```，一般寄存器(general-purpose registers)```R4-R15```。

在这些寄存器中，寄存器```R15```用来在函数间传参。

### 陌生的指令

#### 1. ```mov.b  @r11, r15```

这里的```@r11``` 用来取出 寄存器R11的值 指向位置的值，即是：```Mem(r11)```

这里的```.b``` 表示操作数大小为字节，同时有```.w```，表示操作数大小为两个字节，在默认情况下，采用```.w```方式。

为了更加形象的说明，这里举一个例子。

指令执行前：

``` assembly
r11 = 0010
r15 = fa16
Mem(0010) = 0ff4
```

指令执行后：

```assembly
r11 = 0010
r15 = faf4
Mem(0010) = 0ff4
```

#### 2. ```sxt r15```

本指令在这里是将```r15```的第7位复制到第8到第15位。

本指令的功能是扩展为16位

用另外一种方法来描述即为：

```python
def sxt(x):
    if x%256 > 127:
        x |= 0xff00
    else:
        x &= 0x00ff
     
```

#### 3. ```call <INT>```

这条指令是调用软中断，在调用之前，需要压入调用号。针对不同的调用号还可能需要压入数据已供使用。

从分析第一个软件中，可以得到以下几个调用，其他的<font color=red>[尚待补充]</font>。

```assembly
输出中断：
push	#number
push	#0x0
call	<INT>
```

```assembly
输入单字符中断：
push	#0x1
call	<INT>
```

```assembly
输入多个字符中断：
push	#number
push	#0x2
call	<INT>
```

```assembly
打开门锁中断：
push	#0x7f
call	<INT>
```

## 汇编分析

如果单说解出这道题目，是很容易的。但是本文的目的不在此。本着学习的原则，尽量的磨炼自己。

### main函数

```assembly
4438 <main>
4438:  3150 9cff      add   #0xff9c, sp 
443c:  b012 7e44      call  #0x447e <create_password>
4440:  3f40 e444      mov   #0x44e4 "Enter the password to continue", r15 
4444:  b012 9445      call  #0x4594 <puts>
4448:  0f41           mov   sp, r15
444a:  b012 b244      call  #0x44b2 <get_password>
444e:  0f41           mov   sp, r15
4450:  b012 bc44      call  #0x44bc <check_password>
4454:  0f93           tst   r15 // r15 used to stored returned value
4456:  0520           jnz   #0x4462 <main+0x2a>
4458:  3f40 0345      mov   #0x4503 "Invalid password; try again.", r15
445c:  b012 9445      call  #0x4594 <puts>
4460:  063c           jmp   #0x446e <main+0x36>
4462:  3f40 2045      mov   #0x4520 "Access Granted!", r15
4466:  b012 9445      call  #0x4594 <puts>
446a:  b012 d644      call  #0x44d6 <unlock_door>
446e:  0f43           clr   r15
4470:  3150 6400      add   #0x64, sp
4474 <__stop_progExec__>
4474:  32d0 f000      bis   #0xf0, sr   
4478:  fd3f           jmp   #0x4474 <__stop_progExec__+0x0>

```

对应的类c语言表示

```c
main() {
    sp = 0xff9c;
    create_password();

    r15 = 0x44e4;
    puts(r15);
    
    r15 = sp;
    get_password(r15);
      
    res = check_password(r15);
    if (res != 0) {
        r15 = 0x4520;
        puts(r15);
        unlock_door();
    }   
    else {
        r15 = 0x4503;
        puts(r15);
    }
    __stop_progExec()
}

```

我们可以看出，这是一个很简单的c语言程序。现在对里面的函数进一步分析，这里我们只分析两个函数：create_password()和check_password()

### create_password函数

```assembly
447e <create_password>
447e:  3f40 0024      mov   #0x2400, r15
4482:  ff40 6300 0000 mov.b #0x63, 0x0(r15)
4488:  ff40 6700 0100 mov.b #0x67, 0x1(r15)
448e:  ff40 4500 0200 mov.b #0x45, 0x2(r15)
4494:  ff40 4400 0300 mov.b #0x44, 0x3(r15)
449a:  ff40 6600 0400 mov.b #0x66, 0x4(r15)
44a0:  ff40 5600 0500 mov.b #0x56, 0x5(r15)
44a6:  ff40 5a00 0600 mov.b #0x5a, 0x6(r15)
44ac:  cf43 0700      mov.b #0x0, 0x7(r15)
44b0:  3041           ret
```

```c
create_password() {
    r15 = 0x2400;
    *r15 = [0x63, 0x67, ..., 0x5a, 0x00];
}
```

### check_password函数

```assembly
44bc <check_password>
44bc:  0e43           clr   r14
44be:  0d4f           mov   r15, r13
44c0:  0d5e           add   r14, r13
44c2:  ee9d 0024      cmp.b @r13, 0x2400(r14)
44c6:  0520           jne   #0x44d2 <check_password+0x16>
44c8:  1e53           inc   r14
44ca:  3e92           cmp   #0x8, r14
44cc:  f823           jne   #0x44be <check_password+0x2>
44ce:  1f43           mov   #0x1, r15
44d0:  3041           ret
44d2:  0f43           clr   r15
44d4:  3041           ret
```

```c
int check_pasword(r15) {
    p = r15
    while(*(0x2400 + idx) == *(p + idx)) {
        idx ++;
        if(idx == 8)
            return 1;
    }
    return 0
}
```

## 结论

通过对以上的函数分析，我们可以很容易的得到```password=cgEDfVZ```

在输入栏输入```solve```,然后输入```cgEDfVZ```。

我们将进入[No.2]()关。

---

**reference**

1. CPE 323 MSP430 INSTRUCTION SET ARCHITECTURE by Aleksandar Milenkovic
