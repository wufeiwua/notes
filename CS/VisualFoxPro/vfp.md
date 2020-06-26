# VFP 学习简单理解

## 前言

如果想深入学习VFP，包括软件的使用戳[这里](http://garfieldhudson.com/FreeVideos.aspx),可能需要翻墙

`Visual FoxPro`（简称VFP）。是微软公司的开发的运行于Windows平台的一个 32 位数据库管理系统。官网几年前也放弃了维护和跟新，转去`.NET` 了。VFP 是典型的关系型数据库，支持面向对象的程序设计，语法类似VB。

## VFP程序设计

- VPF程序文件以后缀`.prg` 结尾，prg是program的缩写。
- VFP中语法对大小写不敏感，所以大写小写看个人习惯

### 显示与注释

- `*`或者`NOTE`。 行首注释。
- `&&`。行尾注释,注释的内容不会被执行和编译。
- `?`。显示(打印)值。换行打印
- `??`。不换行打印
- `say`。指定位置输出

### 数据类型

数据类型有：

- 字符（字符串）。 用`""` 或者 `''`或者`[]`括起的，例如`"hello word"` `'a'` `'b'`;
- 货币。货币型用`$`开头，`$23`
- 日期。日期型数据必须用`{}`定界，例如`{^2012-12-24}`
- 时间。时间类型也必须用`{}`定界，例如`{^2012-12-31 12:59:59}`
- 数值。1,2,3.1415926
- 逻辑。通常称为布尔值，真或假，是或否。例如`.f.`,`.t.`,`.y.`,`.n.`
- 通用。
- 备注。

### 常量和变量

#### 常量

常量的值不会发生变化。像`"hello"` `{19-01-14}` `.t.` `2019`;
就是数字，字符一类的东西

VFP中常量分为四种类型：
- `"hello"` 一类的 **字符型**；
- `2019`  一类的 **数值型**;
- `{09-01-14}` 一类的**日期型**;
- `.t.` `.f.` `.Y.` `.N.`一类的**逻辑型**表示`是`或`否`,`真`或`假`。逻辑型常量需要用`.`（点，dot）来定界。否则不是逻辑常量；

还有一种编译时的常量用`#DEFINE`来定义,`#DEFINE PI = 3.1415926`

#### 变量

变量在程序运行时会发生变化。通常是常量赋值给变量
VFP中变量分为`字段变量`和`内存变量`：

1. 字段变量是指数据表中已经定义的，由表的记录指针访问。
2. 像这样`var = 3`，其中 `var` 我们称为变量，`3` 称为常量，`=`是一种赋值操作；

  `var = 3` 称为语句，后面会讲到。
  现在我们定义了一个变量，取名为`var`，并将`3`赋值给`var` 所以，`var` 现在的值是`3`，
  我们还可以将 `var = 4`，此时 `var` 的值是 `4`，这一过程称为 `变量声明`或者叫`变量创建`.

#### 字段变量与指针

- 在表中，已经定义的数据项称为字段变量。每一个字段可能有不同的值。
- 在表中，有一个`记录指针`，指针所指向的字段就是改字段的值。
- 指针可以移动，指向不同的字段，*表刚打开时候指针指向第一条记录*

 *指针的移动：*

1. `GO TOP` 移动到第一条记录。
2. `GO BOTTOM` 移动到最后一条记录。
3. `GO num` 移动到指定条目。`GO 1` 移动到第一条，`GO 3`移动到第三条
4. `SKIP num` 相对于当前位置移动。例：当前位置为第3条记录，`SKIP 2`向下移动2条，此时指针指向第5条；
   `SKIP -1`向上移动1条，此时指针指向第4条记录。

*SKIP*如果后面没有给定指定数字，默认向下移动一条记录

```
USE table_name
?recno()			&&显示1
skip 2				&&向下移动两条记录，指向第3条
go bottom			&&移动到最后一条记录
?recno()			&&显示最后一条记录的记录号
skip				&&向下移动一条记录
?eof()				&&是否超过了最后一条记录
```
上面代码用到了两个常用[函数](#函数)`recno()` 和`eof()`。

- 带`recno` 和 `eof` 被称为函数名，`()`称为函数引用。
- `recno()`函数返回当前记录是第几条
- `eof()`函数返回指针是否超过了最后一条记录，如果超过了返回 `.t.` 否则返回`.f.`

#### 变量的声明

变量的声明有两种方式：

- 用`=`赋值操作符。`var = 1`
- 用 `STORE` 。`STORE 1 TO var`

上面两个语句做了同样的事情:

1. 声明变量`var`
2. 给`var`赋值`1`

两个操作是一起进行的。

VFP 中 可以 `var,var2,var3` 声明3 个变量但是都没有赋值。

#### 变量名
- VFP中变量名以汉字、字母、数字或下划线组成，长度最多为10个字节(10个英文字符，5个汉字，因为一个汉字占2字节)
- 变量名必须以字母或汉字开头

#### 变量的访问

声明了变量当然要访问。VFP提供了两种访问(也可以称为，引用)变量的方法。

- `M.var`
- `M->var`

M 是 memory（内存）的简写。访问内存变量,字段变量由表中的记录指针访问,指针指向哪个字段访问的就是哪个值。 

另外：如果字段变量名和内存变量名一样了，那么字段变量名的优先级高于内存变量。这个时候访问内存变量就需要使用 `m.`

#### 变量的作用域

作用域：作用域好比权限，规定了该变量对哪些部分是可见(可以使用)的。

1. 局部变量。VFP的局部变量作用域是*创建这个变量的文件中*，这意味着只有在这个`.prg`的文件中可以使用和修改这个变量。
2. 私有变量。私有变量只能在`一段代码块`中使用和修改。
3. 全局变量。全局变量对所有的程序都是可见的。所有的程序都可以使用。如果没有特殊说明，在命令窗口中创建的
   任何变量都是全局变量，也包括[数组](#数组)。

VFP变量由3个关键词，也叫表达式，`LOCAL`，`PRIVATE`，`PUBLIC`定义,根据单词的含义就很好理解：

- `LOCAL` 局部变量
- `PRIVATE` 私有变量
- `PUBLIC` 全局变量

```
&&带属性的变量声明
PRIVATE var1 = 1
PUBLIC var2 = 2
LOCAL var3 = 3
```
#### 数组
- 数组是数据的集合
- 数组是多个不同的变量的集合。这些变量有不同的变量名，但是拥有不同的下标。

VFP中有三种类型的数组：
- 一维数组
- 二维数组
- 三维数组

我们不需要深刻学习三维数组。一般都只是用到一维数组和二维数组。

**数组的声明**

- VFP中可以通过`DECLARE` 和 `DEMENSION` 关键字 声明一个或者多个数组
- VFP中数组的下标是从`1`开始的
- 不需要声明数组数据类型
- 数组的数据类型依赖与本身的数据
- 数组可以使用 `[]` 也可以使用 `()`
- 数组默认的数据类型是`逻辑型`
- 在声明数组后可以随时的改变数组的大小

**值得注意的是，在数组声明时必须给定数组的大小**

```
DEMENSION arr 1 = [2]   &&声明了一个长度为2的一维数组
DECLARE arr2 = (4)      &&声明了一个长度为4的一维数组

DEMENSION arr = [2][3] &&声明了一个长度为6 的二维数组
```
上面程序中，arr 是一个二维数组。是由 2 个 一维数组构成，每个一维数组的长度都是3。

||||
:-------:|:-------:|:-------:
arr[1][1]|arr[1][2]|arr[1][3]
arr[2][1]|arr[2][2]|arr[2][3]

其次，在内存中二维数组是线性排列的，这意味着,其实在内存中的排列是

|||||||
:-------:|:-------:|:-------:|:-------:|:-------:|:-------:
arr[1][1]|arr[1][2]|arr[1][3]|arr[2][1]|arr[2][2]|arr[2][3]

对于数组的值的访问，由数组名加上下标进行访问，提醒一下，VFP中数组的下标从 `1` 开始。
所以访问数组的第一个元素 是 `arr[1]` 。对于二维数组而言，可以用两个 `[]`, 也可以用一个。
例如：`arr[1][2]` 访问二维数组第一个数组的第二个元素，也可以写成，`arr[2]`。
`arr[2][1]` 访问二维数组中第二个数组的第一个元素也可以写成`arr[4]`。
这是因为数组在内存中是线性排列的原因。

可以看看[C语言中指针与数组的关系](../C/C-point-array-string.md)

### VFP流程控制

#### IF...ELSE...ENDIF
基本分支结构，没啥好讲的

- `IF` 就是如果怎么怎么样。if i were u ,i would bala bala;
- `ELSE` 否则就怎么怎么样。
- `ENDIF` 和 `IF` 配套使用，有`IF`就得有`ENDIF`。告诉程序什么时候结束。

对于像VFP这样的语言，没有`{`,`}`作为块的说明。所以只有`END-`语句来代替。

举个栗子：

```
a = 0
b = 1
if a > b
?m.a
else
?m.b
endif
```
首先声明两个变量a和b，分别赋值 0 和 1。分之开始，如果 a 大于 b 显示 a 的值。否则显示b的值。分之结束

#### DO CASE...ENDCASE
do case，多条件分支。和 if 、else 几乎等价。
case 后面跟的是一个条件判定,然后是表达式
例子：
```
a = 2
do case 
   case a == 1
        ?"a is 1"
   case a == 2
       ?"a is 2"
   case a == 3
        ?"a is 3"
   otherwise
        ?"a is not in 1 , 2 ,3"
endcase
```
otherwise 就是上面的条件都不满足的时候执行的。
同样的 do case 最后必须跟 endcase 语句告诉程序什么时候该停止


#### DO WHILE...ENDDO
`DO WHILE` 是一种循环结构。可以在某个特定条件下循环执行,do while 称为条件循环
例如：
```
a = 1
do while a < 5
    ?"hello world!!!"
    a = a + 1 
enddo
```
程序循环打印4次 `hello world`
1. 第一次 a = 1 ，1 < 5 ，打印 hello world,然后执行  a = a + 1 ,此时 a = 2
2. 第二次 a = 2 ，2 < 5 , 打印 hello world，执行     a = a + 1，此时 a = 3
3. 第三次 a = 3， 3 < 5 ，打印 hello world ，执行    a = a + 1，此时 a = 4
4. 第四次 a = 4， 4 < 5 ，打印 hello world ，执行    a = a + 1，此时 a = 5
5. 第五次 a = 5 ，5 等于 5 ，条件为假，所以什么也不执行，enddo 结束 循环。

> VFP 中不支持变量自增和自减,例如 a++,a--,++a,--a等操作。

#### FOR...ENDFOR
`FOR`循环和`DO WHILE`循环类似，都是循环结构。for循环被称为步长循环

```
clear
for n = 1 to 5
    ?"hello world"
endfor
```
程序会打印5次 hello world。
如果把 for 循环写成 do while 循环是这样的：

```
clear 
n = 1
do while n <= 5
   ?"hello world"
   n = n + 1
enddo
```
`FOR`循环可以设置步长，所谓步长就是 n = n + 步长;默认步长 是 1 ，即 n = n + 1
表示下一个值是多少。

```
clear 
for n = 2 to 10 step 2
?"hello world"
endfor
```
就和

```
clear
n = 2
do while n <= 10
    ?"hello world"
    n = n + 2
enddo
```
是等价的


#### SCAN...ENDSCAN
`scan`是操作指针的循环所以跟表有关联。`scan` 循环中会自动执行 `SKIP` 操作

```
clear 
use table_name
scan
  display
endscan
```
上面代码就是打印出表中所有数据

如果换成 do while 循环 就是

```
clear
use table_name
do while not eof()
display
skip
```
如果没有到超过最后一条记录，就显示，然后指针下移1 位

> 循环中可以嵌套其他循环


#### LOOP 与 EXIT

`LOOP` 和 `EXIT` 在C语言中和其他大多数主流高级语言中对应 `continue` 和 `break` ,主要是在循环中使用。

1. `loop`跳出当前循环，如果条件成立`loop`后面的代码不执行，并开始下一循环
2. `exit`跳出整个循环，如果条件成立整个循环都不执行了；

下面用 do while 循环示例：

```
clear
n = 0
do while n < 10
    n = n + 1

    if n == 3
    loop
    endif

    if n == 8
    exit
    endif
    
    ?n
enddo
```

程序打印 1,2,4,5,6,7
也就是，当 n 等于 3 的时候 下面的代码都不走了，然后继续 n = n + 1,就是 n = 3 + 1 等于 4。
当 n 等于 8 的时候 整个程序都退出

### 函数

函数的声明用 `FUNCTION` 和 `ENDFUNC` 构成
在`function`和`endfunc` 中间的称为函数体。
一般可以不用`endfunc`，因为有`return`语句作为返回

`return`语句可以返回一个值，也可以什么都不返回

- 函数与主程序在一个文件中
- 函数可以接受参数也可以不接收参数
- 函数的调用可以用`name()` 的形式 也可以用 `do <name> with <params>`
- 参数之间用`,`隔开

```
re = myFunc(2,3)
?re

function myFunc
parameters a,b
if a > b
return a
endif
return b
endfunc
```
myFunc 称为函数名

### 子程序

子程序是一个程序文件，它与主程序的主要区别：主程序只能调用其他程序而不能被其他程序调用。
子程序既能被主程序调用也能被其他子程序调用。

- 子程序的定义用 `parameters` 定义，后面可以跟参数
- 子程序需要`return`语句
- 子程序的调用用`do <name> with <params>`
- 参数之间用`,`隔开

例如：zcx1.prg

```
parameters a,b,c
a = b + c
return
```

在主程序中调用子程序用 `do <name> with <params>`

例如：

```
a = 0
b = 1 
c = 2
do zcx1 with a,b,c
?c
```

### 过程

- 过程的声明和函数类似用`PROCEDURE` 和`ENDPROC` 包含的块。
- 过程与子程序类似，但是过程可以与主程序在一个文件中也可以不在一个文件中。
- 如果不在一个程序中 需要用`SET PROVEDURE TO [<过程文件名列表>][ADDITIVE]`打开过程文件。
- 过程的调用可以用`name()` 的形式 也可以用 `do <name> with <params>`
- 参数之间用`,`隔开

```
re = myProc(2,3)
?re
procedure myProc 
parameters a,b  
return a + b
endproc
```

```
re = 0
do myProc with 1,2,re
?re
procedure myProc 
parameters a,b,c 
c = a + b
return 
endproc          
```
### 实参与形参

### VFP常用函数

### VFP常用命令


|命令|描述|
|:----|:----|
|[? ](#VFP常用函数)                        				|在下一行显示表达式串 |
|[?? ](#VFP常用函数)                       				|在当前行显示表达式串 |
|[@... ](#VFP常用函数)                     				|将数据按用户设定的格式显示在屏幕上或在打印机上打印 |
|[ACCEPT](#VFP常用函数)                    				|把一个字符串赋给内存变量 |
|[APPEND](#VFP常用函数)                    				|给数据库文件追加记录 |
|[APPEND FROM](#VFP常用函数)               				|从其它库文件将记录添加到数据库文件中 |
|[AVERAGE](#VFP常用函数)                   				|计算数值表达式的算术平均值 |
|[BROWSE](#VFP常用函数)                    				|全屏幕显示和编辑数据库记录 |
|[CALL](#VFP常用函数)                      				|运行内存中的二进制文件 |
|[CANCEL](#VFP常用函数)                    				|终止程序执行，返回圆点提示符 |
|[CASE](#VFP常用函数)                      				|在多重选择语句中，指定一个条件 |
|[CHANGE](#VFP常用函数)                    				|对数据库中的指定字段和记录进行编辑 |
|[CLEAR](#VFP常用函数)                     				|清洁屏幕，将光标移动到屏幕左上角 |
|[CLEAR ALL](#VFP常用函数)                 				|关闭所有打开的文件，释放所有内存变量，选择1号工作区 |
|[CLEAR FIELDS](#VFP常用函数)              				|清除用SET FIELDS TO命令建立的字段名表 |
|[CLEAR GETS](#VFP常用函数)                				|从全屏幕READ中释放任何当前GET语句的变量 |
|[CLEAR MEMORY](#VFP常用函数)              				|清除当前所有内存变量 |
|[CLEAR PROGRAM](#VFP常用函数)             				|清除程序缓冲区 |
|[CLEAR TYPEAHEAD](#VFP常用函数)           				|清除键盘缓冲区 |
|[CLOSE](#VFP常用函数)                     				|关闭指定类型文件 |
|[CONTINUE](#VFP常用函数)                  				|把记录指针指到下一个满足LOCATE命令给定条件的记录，在LOCATE命令后出现。无LOCATE则出错 |
|[COPY TO](#VFP常用函数)                   				|将使用的数据库文件复制另一个库文件或文本文件 |
|[COPY FILE](#VFP常用函数)                 				|复制任何类型的文件 |
|[COPY STRUCTURE EXTENED TO](#VFP常用函数) 				|当前库文件的结构作为记录，建立一个新的库文件 |
|[COPY STRUCTURE TO](#VFP常用函数)         				|将正在使用的库文件的结构复制到目的库文件中 |
|[COUNT](#VFP常用函数)                     				|计算给定范围内指定记录的个数 |
|[CREATE](#VFP常用函数)                    				|定义一个新数据库文件结构并将其登记到目录中 |
|[CREATE FROM](#VFP常用函数)               				|根据库结构文件建立一个新的库文件 |
|[CREATE LABEL](#VFP常用函数)              				|建立并编辑一个标签格式文件 |
|[CREATE REPORT](#VFP常用函数)             				|建立宾编辑一个报表格式文件 |
|[DELETE](#VFP常用函数)                    				|给指定的记录加上删除标记 |
|[DELETE FILE](#VFP常用函数)               				|删除一个未打开的文件 |
|[DIMENSION](#VFP常用函数)                 				|定义内存变量数组 |
|[DIR 或 DIRECTORY](#VFP常用函数)  					|列出指定磁盘上的文件目录 |
|[DISPLAY](#VFP常用函数)                   				|显示一个打开的库文件的记录和字段 |
|[DISPLAY FILES](#VFP常用函数)             				|查阅磁盘上的文件 |
|[DISPLAY HISTORY](#VFP常用函数)           				|查阅执行过的命令 |
|[DISPLAY MEMORY](#VFP常用函数)            				|分页显示当前的内存变量 |
|[DISPLAY STATUS](#VFP常用函数)            				|显示系统状态和系统参数 |
|[DISPLAY STRUCTURE](#VFP常用函数)         				|显示当前书库文件的结构 |
|[DO](#VFP常用函数)                        				|执行FoxBase程序 |
|[DO CASE](#VFP常用函数)                   				|程序中多重判断开始的标志 |
|[DO WHILE](#VFP常用函数)                  				|程序中一个循环开始的标志 |
|[EDIT](#VFP常用函数)                      				|编辑数据库字段的内容 |
|[EJECT](#VFP常用函数)                     				|使打印机换页的命令，将PROW(#VFP常用函数)函数和PCOL(#VFP常用函数)函数值置为0 |
|[ELSE](#VFP常用函数)                      				|在IF...ENDIF结构中提供另一个条件选择路线 |
|[ENDCASE](#VFP常用函数)                   				|终止多重判断 |
|[ENDDO](#VFP常用函数)                     				|程序中一个循环体结束的标志 |
|[ENDIF](#VFP常用函数)                     				|判断体IF...ENDIF结构结束标志 |
|[ERASE](#VFP常用函数)                     				|从目录中删除指定文件 |
|[EXIT](#VFP常用函数)                      				|在循环体内执行退出循环的命令 |
|[FIND](#VFP常用函数)                      				|将记录指针移动到第一个含有与给定字符串一致的索引关键字的记录上 |
|[FLUSH](#VFP常用函数)                     				|清除所有的磁盘存取缓冲区 |
|[GATHER FROM](#VFP常用函数)               				|将数组元素的值赋予数据库的当前记录中 |
|[GO/GOTO](#VFP常用函数)                   				|将记录指针移动到指定的记录号 |
|[HELP](#VFP常用函数)                      				|激活帮助菜单，解释FoxBASE+的命令 |
|[IF](#VFP常用函数)                        				|在IF...ENDIF结构中指定判断条件 |
|[INDEX](#VFP常用函数)                     				|根据指定的关键词生成索引文件 |
|[INPUT](#VFP常用函数)                     				|接受键盘键入的一个表达式并赋予指定的内存变量 |
|[INSERT](#VFP常用函数)                    				|在指定的位置插入一个记录 |
|[JOIN](#VFP常用函数)                      				|从两个数据库文件中把指定的记录和字段组合成另一个库文件 |
|[KEYBOARD](#VFP常用函数)                  				|将字符串填入键盘缓冲区 |
|[LABEL FROM](#VFP常用函数)                				|用指定的标签格式文件打印标签 |
|[LIST](#VFP常用函数)                      				|列出数据库文件的记录和字段 |
|[LIST FILES](#VFP常用函数)                				|列出磁盘当前目录下的文件 |
|[LIST HISTORY](#VFP常用函数)              				|列出执行过的命令 |
|[LIST MEMORY](#VFP常用函数)               				|列出当前内存变量及其值 |
|[LIST STATUS](#VFP常用函数)               				|列出当前系统状态和系统参数 |
|[LIST STRUCTURE](#VFP常用函数)            				|列出当前使用的数据库的库结构 |
|[LOAD](#VFP常用函数)                      				|将汇编语言程序从磁盘上调入内存 |
|[LOCATE](#VFP常用函数)                    				|将记录指针移动到对给定条件为真的记录上 |
|[LOOP](#VFP常用函数)                      				|跳过循环体内LOOP与ENDDO之间的所有语句，返回到循环体首行 |
|[MENU TO](#VFP常用函数)                   				|激活一组@...PROMPT命令定义的菜单 |
|[MODIFY COMMAND](#VFP常用函数)            				|进入FoxBASE+系统的字处理状态，并编辑一个ASCII码文本文件（如果指定文件名以.PRG为后缀，则编辑一个FoxBASE+命令文件） |
|[MODIFY FILE](#VFP常用函数)               				|编辑一个一般的ASCII码文本文件 |
|[MODIFY LABEL](#VFP常用函数)              				|建立并编辑一个标签(.LBL)文件 |
|[MODIFY REPORT](#VFP常用函数)             				|建立并编辑一个报表格式文件(.FRM)文件 |
|[MODIFY STRUCTURE](#VFP常用函数)          				|修改当前使用的库文件结构 |
|[NOTE或`*`](#VFP常用函数)         					|在命令文件（程序）中插入以行注释（本行不被执行） |
|[ON](#VFP常用函数)                        				|根据指定条件转移程序执行 |
|[OTHERWISE](#VFP常用函数)                 				|在多重判断(DO CASE)中指定除给定条件外的其它情况 |
|[PACK](#VFP常用函数)                      				|彻底删除加有删除标记的记录 |
|[PARAMETERS](#VFP常用函数)                				|指定子过程接受主过程传递来的参数所存放的内存变量 |
|[PRIVATE](#VFP常用函数)                   				|定义内存变量的属性为局部性质 |
|[PROCEDURE](#VFP常用函数)                 				|一个子过程开始的标志 |
|[PUBLIC](#VFP常用函数)                    				|定义内存变量为全局性质 |
|[QUIT](#VFP常用函数)                      				|关闭所有文件并退出FoxBASE+ |
|[READ](#VFP常用函数)                      				|激活GET语句，并正是接受在GET语句中输入的数据 |
|[RECALL](#VFP常用函数)                    				|恢复用DELETE加上删除标记的记录 |
|[REINDEX](#VFP常用函数)                   				|重新建立正在使用的原有索引文件 |
|[RELEASE](#VFP常用函数)                   				|清楚当前内存变量和汇编语言子程序 |
|[RENAME](#VFP常用函数)                    				|修改文件名 |
|[REPLACE](#VFP常用函数)                   				|用指定的数据替换数据库字段中原有的内容 |
|[REPORT FORM](#VFP常用函数)               				|显示数据报表 |
|[RESTORE FROM](#VFP常用函数)              				|从内存变量文件(.MEM)中恢复内存变量 |
|[RESTORE SCREEN](#VFP常用函数)            				|装载原来存储过的屏幕映象 |
|[RESUME](#VFP常用函数)                    				|使暂停的程序从暂停的断点继续执行 |
|[RETRY](#VFP常用函数)                     				|从当前执行的子程序返回调用程序，并从原调用行重新执行 |
|[RETURN](#VFP常用函数)                    				|结束子程序，返回调用程序 |
|[RUN/！](#VFP常用函数)            					|在FoxBASE+中执行一个操作系统程序 |
|[SAVE TO](#VFP常用函数)                   				|把当前内存变量及其值存入指定的磁盘文件(.MEM) |
|[SAVE SCREEN](#VFP常用函数)               				|将当前屏幕显示内容存储在指定的内存变量中 |
|[SCATTER](#VFP常用函数)                   				|将当前数据库文件中的数据移到指定的数组中 |
|[SEEK](#VFP常用函数)                      				|将记录指针移到第一个含有与指定表达式相符的索引关键字的记录 |
|[SELECT](#VFP常用函数)                    				|选择一个工作区 |
|[SET](#VFP常用函数)                       				|设置FoxBASE+控制参数 |
|[SET ALTERNATE ON/OFF](#VFP常用函数)      				|设置传送/不传送输出到一个文件中 |
|[SET ALTERNATE TO](#VFP常用函数)          				|建立一个存放输出的文件 |
|[SET BELL ON/OFF](#VFP常用函数)           				|设置输入数据时响铃/不响铃 |
|[SET CARRY ON/OFF](#VFP常用函数)          				|设置最后一个记录复制/不复制到添加的记录中 |
|[SET CENTURY ON/OFF](#VFP常用函数)        				|设置日期型变量要/不要世纪前缀 |
|[SET CLEAR ON/OFF](#VFP常用函数)          				|设置屏幕信息能/不能被清除 |
|[SET COLOR ON/OFF](#VFP常用函数)          				|设置彩色/单色显示 |
|[SET COLOR TO](#VFP常用函数)              				|设置屏幕显示色彩 |
|[SET CONFIRM ON/OFF](#VFP常用函数)        				|设置在全屏幕编辑方式中，要求/不要求自动跳到下一个字段 |
|[SET CONSOLE ON/OFF](#VFP常用函数)        				|设置将输出传送/不传送到屏幕 |
|[SET DATE](#VFP常用函数)                  				|设置日期表达式的格式 |
|[SET DEBUG ON/OFF](#VFP常用函数)          				|设置传送/不传送ECHO的输出到打印机上 |
|[SET DECIMALS TO](#VFP常用函数)           				|设置计算结果需要显示的小数位数 |
|[SET DEFAULT TO](#VFP常用函数)            				|设置默认的驱动器 |
|[SET DELETED ON/OFF](#VFP常用函数)        				|设置隐藏/显示有删除标记的记录 |
|[SET DELIMITER TO](#VFP常用函数)          				|为全屏幕显示字段和变量设置定界符 |
|[SET DELIMITER ON/OFF](#VFP常用函数)      				|选择可选的定界符 |
|[SET DEVICE TO SCREEN/PRINT](#VFP常用函数)				|将@...SAY命令的结果传送到屏幕/打印机 |
|[SET DOHISTORY ON/OFF](#VFP常用函数)      				|设置存/不存命令文件中的命令到历史记录中 |
|[SET ECHO ON/OFF](#VFP常用函数)           				|命令行回送到屏幕或打印机 |
|[SET ESCAPE ON/OFF](#VFP常用函数)         				|允许ESCAPE退出/继续命令文件的执行 |
|[SET EXACT ON/OFF](#VFP常用函数)          				|在字符串的比较中，要求/不要求准确一致 |
|[SET EXACLUSIVE ON/OFF](#VFP常用函数)     				|设置数据库文件的共享 |
|[SET FIELDS ON/OFF](#VFP常用函数)         				|设置当前打开的数据库中部分/全部字段为可用 |
|[SET FIELDS TO](#VFP常用函数)             				|指定打开的数据库中可被访问的字段 |
|[SET FILTER TO](#VFP常用函数)             				|在操作中将数据库中所有不满足给定条件的记录排除 |
|[SET FIXED ON/OFF](#VFP常用函数)          				|固定/不固定显示的小数位数 |
|[SET FORMAT TO](#VFP常用函数)             				|打开指定的格式文件 |
|[SET FUNCTION](#VFP常用函数)              				|设置F1-F9功能键值 |
|[SET HEADING ON/OFF](#VFP常用函数)        				|设置LIST或DISPLAY时，显示/不显示字段名 |
|[SET HELP ON/OFF](#VFP常用函数)           				|确定在出现错误时，是否给用户提示 |
|[SET HISTORY ON/OFF](#VFP常用函数)        				|决定是/否把命令存储起来以便重新调用 |
|[SET HISTORY TO](#VFP常用函数)            				|决定显示历史命令的数目 |
|[SET INDEX TO](#VFP常用函数)              				|打开指定的索引文件 |
|[SET INTENSITY ON/OFF](#VFP常用函数)      				|对全屏幕操作实行/不实行反转显示 |
|[SET MARGIN TO](#VFP常用函数)             				|设置打印机左页边 |
|[SET MEMOWIDTH TO](#VFP常用函数)          				|定义备注型字段输出宽度和REPORT命令隐含宽度 |
|[SET MENU ON/OFF](#VFP常用函数)           				|确定在全屏幕操作中是否显示菜单 |
|[SET MESSAGE TO](#VFP常用函数)            				|定义菜单中屏幕底行显示的字符串 |
|[SET ODOMETER TO](#VFP常用函数)           				|改变TALK命令响应间隔时间 |
|[SET ORDER TO](#VFP常用函数)              				|指定索引文件列表中的索引文件 |
|[SET PATH TO](#VFP常用函数)               				|为文件检索指定路径 |
|[SET PRINT ON/OFF](#VFP常用函数)          				|传送/不传送输出数据到打印机 |
|[SET PRINTER TO](#VFP常用函数)            				|把打印的数据输送到另一种设备或一个文件中 |
|[SET PROCEDURE TO](#VFP常用函数)          				|打开指定的过程文件 |
|[SET RELATION TO](#VFP常用函数)           				|根据一个关键字表达式连接两个数据库文件 |
|[SET SAFETY ON/OFF](#VFP常用函数)         				|设置保护，在重写文件时提示用户确认 |
|[SET SCOREBORAD ON/OFF](#VFP常用函数)     				|设置是/否在屏幕的第0行上显示FoxBASE+的状态信息 |
|[SET STATUS ON/OFF](#VFP常用函数)         				|控制是/否显示状态行 |
|[SET STEP ON/OFF](#VFP常用函数)           				|每当执行完一条命令后，暂停/不暂停程序的执行 |
|[SET TALK ON/OFF](#VFP常用函数)           				|是否将命令执行的结果传送到屏幕上 |
|[SET TYPEAHEAD TO](#VFP常用函数)          				|设置键盘缓冲区的大小 |
|[SET UNIQUE ON/OFF](#VFP常用函数)         				|在索引文件中出现相同关键字的第一个/所有记录 |
|[SKIP](#VFP常用函数)                      				|以当前记录指针为准，前后移动指针 |
|[SORT TO](#VFP常用函数)                   				|根据数据库文件的一个字段或多个字段产生一个排序的文件 |
|[STORE](#VFP常用函数)                     				|赋值语句 |
|[SUM](#VFP常用函数)                       				|计算并显示数据库记录的一个表达式在某范围内的和 |
|[SUSPEND](#VFP常用函数)                   				|暂停（挂起）程序的执行 |
|[TEXT...ENDTEXT](#VFP常用函数)            				|在屏幕上当前光标位置显示...的文本数据块 |
|[TOTAL TO](#VFP常用函数)                  				|对预先已排序的文件产生一个具有总计的摘要文件 |
|[TYPE](#VFP常用函数)                      				|显示ASCII码文件的内容 |
|[UNLOCK](#VFP常用函数)                    				|解除当前库文件对记录和文件的加锁操作 |
|[UPDATE](#VFP常用函数)                    				|允许对一个数据库进行成批修改 |
|[USE](#VFP常用函数)                       				|带文件名的USE命令打开这个数据库文件。无文件名时，关闭当前操作的数据库文件 |
|[WAIT](#VFP常用函数)                      				|暂停程序执行，按任意键继续执行 |
|[ZAP](#VFP常用函数)                       				|删除当前数据库文件的所有记录（不可恢复）|

