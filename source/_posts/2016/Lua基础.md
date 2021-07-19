---
layout: "post"
title: "Lua基础"
date: "2016-09-28 11:46"
categories:
  - Lua
tags:
---

### Lua 基础数据类型

#### nil

nil 是一种类型，Lua 将 nil 用于表示“无效值”。一个变量在第一次赋值前的默认值是 nil，将 nil 赋予给一个全局变量就等同于删除它。

```
local num
print(num)        -->output:nil

num = 10
print(num)        -->output:10
```

#### boolean

布尔类型，可选值 true/false；Lua 中 nil 和 false 为“假”，其它所有值均为“真”。

```
local a = true
local b = 0
local c = nil
if a then
    print("a")        -->output:a
else
    print("not a")    --这个没有执行
end

if b then
    print("b")        -->output:b
else
    print("not b")    --这个没有执行
end

if c then
    print("c")        --这个没有执行
else
    print("not c")    -->output:not c
end
```

#### number（数字）

Number 类型用于表示实数，和 C/C++ 里面的 double 类型很类似。可以使用数学函数 `math.floor`（向下取整）和 `math.ceil`（向上取整）进行取整操作。

```
local order = 3.99
local score = 98.01
print(math.floor(order))   -->output:3
print(math.ceil(score))    -->output:99
```

一般地，Lua 的 number 类型就是用双精度浮点数来实现的。值得一提的是，LuaJIT 支持所谓的“dual-number”（双数）模式，即 LuaJIT 会根据上下文用整型来存储整数，而用双精度浮点数来存放浮点数。+

另外，LuaJIT 还支持“长长整型”的大整数（在 x86_64 体系结构上则是 64 位整数）。例如
`print(9223372036854775807LL - 1) -->output:9223372036854775806LL`

#### String (字符串)

Lua 中有三种方式表示字符串:
1、使用一对匹配的单引号。例：'hello'。
2、使用一对匹配的双引号。例："abclua"。
3、字符串还可以用一种长括号（即`[[ ]]）`括起来的方式定义。 我们把两个正的方括号（即`[[` ）间插入 n 个等号定义为第 n 级正长括号。 就是说，0 级正的长括号写作 `[[` ， 一级正的长括号写作 `[=[` ，如此等等。 反的长括号也作类似定义； 举个例子，4 级反的长括号写作 `]====]` 。 一个长字符串可以由任何一级的正的长括号开始，而由第一个碰到的同级反的长括号结束。 整个词法分析过程将不受分行限制，不处理任何转义符，并且忽略掉任何不同级别的长括号。 这种方式描述的字符串可以包含任何东西，当然本级别的反长括号除外。 例：`[[abc\nbc]]`，里面的 `"\n"` 不会被转义。
另外，Lua 的字符串是不可改变的值，不能像在 c 语言中那样直接修改字符串的某个字符，而是根据修改要求来创建一个新的字符串。Lua 也不能通过下标来访问字符串的某个字符。

```
local str1 = 'hello world'
local str2 = "hello lua"
local str3 = [["add\name",'hello']]
local str4 = [=[string have a [[]].]=]

print(str1)    -->output:hello world
print(str2)    -->output:hello lua
print(str3)    -->output:"add\name",'hello'
print(str4)    -->output:string have a [[]].
```

在 Lua 实现中，Lua 字符串一般都会经历一个“内化”（intern）的过程，即两个完全一样的 Lua 字符串在 Lua 虚拟机中只会存储一份。每一个 Lua 字符串在创建时都会插入到 Lua 虚拟机内部的一个全局的哈希表中。 这意味着

1. 创建相同的 Lua 字符串并不会引入新的动态内存分配操作，所以相对便宜（但仍有全局哈希表查询的开销），
2. 内容相同的 Lua 字符串不会占用多份存储空间，
3. 已经创建好的 Lua 字符串之间进行相等性比较时是 O(1) 时间度的开销，而不是通常见到的 O(n).

#### table(表)

Table 类型实现了一种抽象的“关联数组”。“关联数组” 是一种具有特殊索引方式的数组，索引通常是字符串（string）或者 number 类型，但也可以是除 nil 以外的任意类型的值。

```
local corp = {
    web = "www.google.com",   --索引为字符串，key = "web",
                              --            value = "www.google.com"
    telephone = "12345678",   --索引为字符串
    staff = {"Jack", "Scott", "Gary"}, --索引为字符串，值也是一个表
    100876,              --相当于 [1] = 100876，此时索引为数字
                         --      key = 1, value = 100876
    100191,              --相当于 [2] = 100191，此时索引为数字
    [10] = 360,          --直接把数字索引给出
    ["city"] = "Beijing" --索引为字符串
}

print(corp.web)               -->output:www.google.com
print(corp["telephone"])      -->output:12345678
print(corp[2])                -->output:100191
print(corp["city"])           -->output:"Beijing"
print(corp.staff[1])          -->output:Jack
print(corp[10])               -->output:360
```

在内部实现上，table 通常实现为一个哈希表、一个数组、或者两者的混合。具体的实现为何种形式，动态依赖于具体的 table 的键分布特点。

#### function (函数)

在 Lua 中，函数 也是一种数据类型，函数可以存储在变量中，可以通过参数传递给其他函数，还可以作为其他函数的返回值。

```
local function foo()
    print("in the function")
    --dosomething()
    local x = 10
    local y = 20
    return x + y
end

local a = foo    --把函数赋给变量

print(a())

--output:
in the function
30
有名函数的定义本质上是匿名函数对变量的赋值。为说明这一点，考虑
function foo()
end
等价于

foo = function ()
end
类似地，

local function foo()
end
等价于

local foo = function ()
end
```

### Lua 表达式

#### 算数表达式

`+ - * / % ^` ^指数

```
print(1 + 2)       -->打印 3
print(5 / 10)      -->打印 0.5。 这是Lua不同于c语言的
print(5.0 / 10)    -->打印 0.5。 浮点数相除的结果是浮点数
-- print(10 / 0)   -->注意除数不能为0，计算的结果会出错
print(2 ^ 10)      -->打印 1024。 求2的10次方

local num = 1357
print(num % 2)       -->打印 1
print((num % 2) == 1) -->打印 true。 判断num是否为奇数
print((num % 5) == 0)  -->打印 false。判断num是否能被5整数
```

#### 关系运算符

`< > <= >= == ~=` ~= 不等于

```
print(1 < 2)    -->打印 true
print(1 == 2)   -->打印 false
print(1 ~= 2)   -->打印 true
local a, b = true, false
print(a == b)  -->打印 false
```

> 在使用“==”做等于判断时，要注意对于 table, userdate 和函数， Lua 是作引用比较的。也就是说，只有当两个变量引用同一个对象时，才认为它们相等。可以看下面的例子：

```
local a = { x = 1, y = 0}
local b = { x = 1, y = 0}
if a == b then
  print("a==b")
else
  print("a~=b")
end

---output:
a~=b
```

由于 Lua 字符串总是会被“内化”，即相同内容的字符串只会被保存一份，因此 Lua 字符串之间的相等性比较可以简化为其内部存储地址的比较。这意味着 Lua 字符串的相等性比较总是为 O(1). 而在其他编程语言中，字符串的相等性比较则通常为 O(n)，即需要逐个字节（或按若干个连续字节）进行比较。

#### 逻辑运算符

`and or not` 与 或 非

Lua 中的 and 和 or 是不同于 c 语言的。在 c 语言中，and 和 or 只得到两个值 1 和 0，其中 1 表示真，0 表示假。而 Lua 中 and 的执行过程是这样的：

`a and b` 如果 a 为 nil，则返回 a，否则返回 b;
`a or b`如果 a 为 nil，则返回 b，否则返回 a。

> 所有逻辑操作符将 false 和 nil 视作假，其他任何值视作真，对于 and 和 or，“短路求值”，对于 not，永远只返回 true 或者 false。

#### 字符串连接

在 Lua 中连接两个字符串，可以使用操作符“..”（两个点）。如果其任意一个操作数是数字的话，Lua 会将这个数字转换成字符串。注意，连接操作符只会创建一个新字符串，而不会改变原操作数。也可以使用 string 库函数 string.format 连接字符串。

```
print("Hello " .. "World")    -->打印 Hello World
print(0 .. 1)                 -->打印 01

str1 = string.format("%s-%s","hello","world")
print(str1)              -->打印 hello-world

str2 = string.format("%d-%s-%.2f",123,"world",1.21)
print(str2)              -->打印 123-world-1.21
```

由于 Lua 字符串本质上是只读的，因此字符串连接运算符几乎总会创建一个新的（更大的）字符串。这意味着如果有很多这样的连接操作（比如在循环中使用 .. 来拼接最终结果），则性能损耗会非常大。在这种情况下，推荐使用 table 和 table.concat() 来进行很多字符串的拼接，例如：

```
local pieces = {}
for i, elem in ipairs(my_list) do
    pieces[i] = my_process(elem)
end
local res = table.concat(pieces)
```

当然，上面的例子还可以使用 LuaJIT 独有的 table.new 来恰当地初始化 pieces 表的空间，以避免该表的动态生长。这个特性我们在后面还会详细讨论。

#### 优先级

Lua 操作符的优先级如下表所示(从高到低)：

```
^
not # -
* / %
+ -
..
< > <= >= == ~=
and
or
```

```
local a, b = 1, 2
local x, y = 3, 4
local i = 10
local res = 0
res = a + i < b/2 + 1  -->等价于res =  (a + i) < ((b/2) + 1)
res = 5 + x^2*8        -->等价于res =  5 + ((x^2) * 8)
res = a < y and y <=x  -->等价于res =  (a < y) and (y <= x)
```

若不确定某些操作符的优先级，就应显示地用括号来指定运算顺序。这样做还可以提高代码的可读性。

### Lua 的控制结构

流程控制语句对于程序设计来说特别重要，它可以用于设定程序的逻辑结构。一般需要与条件判断语句结合使用。Lua 语言提供的控制结构有 if，while，repeat，for，并提供 break 关键字来满足更丰富的需求。本章主要介绍 Lua 语言的控制结构的使用。

#### if/else

if-else 是我们熟知的一种控制结构。Lua 跟其他语言一样，提供了 if-else 的控制结构。因为是大家熟悉的语法，本节只简单介绍一下它的使用方法。

##### 单个 if 分支

```
local x = 10
if x > 0 then
  print('x is a positive number')
end
```

##### 两个分支 if-else 型

```
x = 10
if x > 0 then
    print("x is a positive number")
else
    print("x is a non-positive number")
end
```

##### 多个分支 if-elseif-else

```
local score = 90
if score == 100 then
    print("Very good!Your score is 100")
elseif score >= 60 then
    print("Congratulations, you have passed it,your score greater or equal to 60")
--此处可以添加多个elseif
else
    print("Sorry, you do not pass the exam! ")
end
```

与 C 语言的不同之处是 else 与 if 是连在一起的，若将 else 与 if 写成 "else if" 则相当于在 else 里嵌套另一个 if 语句，如下代码：

```
score = 0
if score == 100 then
    print("Very good!Your score is 100")
elseif score >= 60 then
    print("Congratulations, you have passed it,your score greater or equal to 60")
else
    if score > 0 then
        print("Your score is better than 0")
    else
        print("My God, your score turned out to be 0")
    end --与上一示例代码不同的是，此处要添加一个end
end
```

#### while

Lua 跟其他常见语言一样，提供了 while 控制结构，语法上也没有什么特别的。但是没有提供 do-while 型的控制结构，但是提供了功能相当的 repeat。+

while 型控制结构语法如下，当表达式值为假（即 false 或 nil）时结束循环。也可以使用 break 语言提前跳出循环。

```
while 表达式 do
--body
end
```

> 示例代码，求 1 + 2 + 3 + 4 + 5 的结果

```
x = 1
sum = 0

while x <= 5 do
    sum = sum + x
    x = x + 1
end
print(sum)  -->output 15
```

值得一提的是，Lua 并没有像许多其他语言那样提供类似 continue 这样的控制语句用来立即进入下一个循环迭代（如果有的话）。因此，我们需要仔细地安排循环体里的分支，以避免这样的需求。
没有提供 continue ，却也提供了另外一个标准控制语句 break ，可以跳出当前循环。例如我们遍历 table ，查找值为 11 的数组下标索引：

```
local t = {1, 3, 5, 8, 11, 18, 21}

local i
for i, v in ipairs(t) do
    if 11 == v then
        print("index[" .. i .. "] have right value[11]")
        break
    end
end
```

#### repeat

Lua 中的 repeat 控制结构类似于其他语言（如：C++语言）中的 do-while，但是控制方式是刚好相反的。简单点说，执行 repeat 循环体后，直到 until 的条件为真时才结束，而其他语言（如：C++语言）的 do-while 则是当条件为假时就结束循环。

```
x = 10
repeat
  print(x)
until true
```

除此之外，repeat 与其他语言的 do-while 基本是一样的。同样，Lua 中的 repeat 也可以在使用 break 退出。

#### for

##### for 数字型

> for 语句有两种形式：数字 for（numeric for）和范型 for（generic for）。

数字型 for 的语法如下：

```
for var = begin, finish, step do
    --body
end
```

关于数字 for 需要关注以下几点：

1. var 从 begin 变化到 finish，每次变化都以 step 作为步长递增 var
2. begin、 finish、 step 三个表达式只会在循环开始时执行一次
3. 第三个表达式 step 是可选的， 默认为 1
4. 控制变量 var 的作用域仅在 for 循环内，需要在外面控制，则需将值赋给一个新的变量
5. 循环过程中不要改变控制变量的值，那样会带来不可预知的影响

```
for i = 1, 5 do
  print(i)
end

-- output:
1
2
3
4
5
```

如果不想给循环设置上限的话，可以使用常量 math.huge：

```
for i = 1, math.huge do
    if (0.3*i^3 - 20*i^2 - 500 >=0) then
      print(i)
      break
    end
end
```

##### for 泛型

泛型 for 循环通过一个迭代器（iterator）函数来遍历所有值：

```
-- 打印数组a的所有值
local a = {"a", "b", "c", "d"}
for i, v in ipairs(a) do
  print("index:", i, " value:", v)
end

-- output:
index:  1  value: a
index:  2  value: b
index:  3  value: c
index:  4  value: d
```

Lua 的基础库提供了 `ipairs`，这是一个用于遍历数组的迭代器函数。在每次循环中，i 会被赋予一个索引值，同时 v 被赋予一个对应于该索引的数组元素值。
下面是另一个类似的示例，演示了如何遍历一个 table 中所有的 key

```
-- 打印table t中所有的key
for k in pairs(t) do
    print(k)
end
```

从外观上看泛型 for 比较简单，但其实它是非常强大的。通过不同的迭代器，几乎可以遍历所有的东西， 而且写出的代码极具可读性。标准库提供了几种迭代器，包括用于迭代文件中每行的（`io.lines`）、 迭代 table 元素的（`pairs`）、迭代数组元素的（`ipairs`）、迭代字符串中单词的（`string.gmatch`）等。
泛型 for 循环与数字型 for 循环有两个相同点：
（1）循环变量是循环体的局部变量；
（2）决不应该对循环变量作任何赋值。

对于泛型 for 的使用，再来看一个更具体的示例。假设有这样一个 table，它的内容是一周中每天的名称：

```
local days = {
  "Sunday", "Monday", "Tuesday", "Wednesday",
  "Thursday", "Friday", "Saturday"
}
```

现在要将一个名称转换成它在一周中的位置。为此，需要根据给定的名称来搜索这个 table。然而 在 Lua 中，通常更有效的方法是创建一个“逆向 table”。例如这个逆向 table 叫 revDays，它以 一周中每天的名称作为索引，位置数字作为值：

```
local revDays = {
   ["Sunday"] = 1,
   ["Monday"] = 2,
   ["Tuesday"] = 3,
   ["Wednesday"] = 4,
   ["Thursday"] = 5,
   ["Friday"] = 6,
   ["Saturday"] = 7
 }
```

接下来，要找出一个名称所对应的需要，只需用名字来索引这个 reverse table 即可：

```
local x = "Tuesday"
print(revDays[x])  -->3
```

当然，不必手动声明这个逆向 table，而是通过原来的 table 自动地构造出这个逆向 table：

```
local days = {
   "Monday", "Tuesday", "Wednesday", "Thursday",
   "Friday", "Saturday","Sunday"
}

local revDays = {}
for k, v in pairs(days) do
  revDays[v] = k
end

-- print value
for k,v in pairs(revDays) do
  print("k:", k, " v:", v)
end

-- output:
k:  Tuesday   v: 2
k:  Monday    v: 1
k:  Sunday    v: 7
k:  Thursday  v: 4
k:  Friday    v: 5
k:  Wednesday v: 3
k:  Saturday  v: 6
```

这个循环会为每个元素进行赋值，其中变量 k 为 key(1、2、...)，变量 v 为 value("Sunday"、"Monday"、...)。
值得一提的是，在 LuaJIT 2.1 中，ipairs() 内建函数是可以被 JIT 编译的，而 pairs() 则只能被解释执行。因此在性能敏感的场景，应当合理安排数据结构，避免对哈希表进行遍历。事实上，即使未来 pairs 可以被 JIT 编译，哈希表的遍历本身也不会有数组遍历那么高效，毕竟哈希表就不是为遍历而设计的数据结构。

##### break/return

**break**

语句 break 用来终止 while、repeat 和 for 三种循环的执行，并跳出当前循环体， 继续执行当前循环之后的语句。下面举一个 while 循环中的 break 的例子来说明：

```
sum = 0
i = 1
while true do
    sum = sum + i
    if sum > 100 then
        break
    end
    i = i + 1
end
print("The result is " .. i)  -->output:The result is 14
```

在实际应用中，break 经常用于嵌套循环中。

**return**

return 主要用于从函数中返回结果，或者用于简单的结束一个函数的执行。

### Lua 函数

在 Lua 中，函数是一种对语句和表达式进行抽象的主要机制。函数既可以完成某项特定的任务，也可以只做一些计算并返回结果。在第一种情况中，一句函数调用被视为一条语句；而在第二种情况中，则将其视为一句表达式。
示例代码：

```
print("hello world!")        -- 用 print() 函数输出 hello world！
local m = math.max(1, 5)     -- 调用数学库函数 max，
                             -- 用来求 1,5 中的最大值，并返回赋给变量 m
```

使用函数的好处：

降低程序的复杂性：把函数作为一个独立的模块，写完函数后，只关心它的功能，而不再考虑函数里面的细节。
增加程序的可读性：当我们调用 math.max() 函数时，很明显函数是用于求最大值的，实现细节就不关心了。
避免重复代码：当程序中有相同的代码部分时，可以把这部分写成一个函数，通过调用函数来实现这部分代码的功能，节约空间，减少代码长度。
隐含局部变量：在函数中使用局部变量，变量的作用范围不会超出函数，这样它就不会给外界带来干扰。

#### 函数的定义

Lua 使用关键字 function 定义函数，语法如下：

```
function function_name (arc)  -- arc 表示参数列表，函数的参数列表可以为空
   -- body
end
```

上面的语法定义了一个全局函数，名为 function_name. 全局函数本质上就是函数类型的值赋给了一个全局变量，即上面的语法等价于

```
function_name = function (arc)
  -- body
end
```

由于全局变量一般会污染全局名字空间，同时也有性能损耗（即查询全局环境表的开销），因此我们应当尽量使用“局部函数”，其记法是类似的，只是开头加上 local 修饰符：

```
local function function_name (arc)
  -- body
end
```

由于函数定义本质上就是变量赋值，而变量的定义总是应放置在变量使用之前，所以函数的定义也需要放置在函数调用之前。

```
local function max(a, b)  --定义函数 max，用来求两个数的最大值，并返回
   local temp = nil       --使用局部变量 temp，保存最大值
   if(a > b) then
      temp = a
   else
      temp = b
   end
   return temp            --返回最大值
end

local m = max(-12, 20)    --调用函数 max，找去 -12 和 20 中的最大值
print(m)                  --> output 20
```

如果参数列表为空，必须使用 () 表明是函数调用。

```
local function func()   --形参为空
    print("no parameter")
end

func()                  --函数调用，圆扩号不能省

--> output：
no parameter
```

在定义函数要注意几点：

1. 利用名字来解释函数、变量的目的，使人通过名字就能看出来函数、变量的作用。
2. 每个函数的长度要尽量控制在一个屏幕内，一眼可以看明白。
3. 让代码自己说话，不需要注释最好。

由于函数定义等价于变量赋值，我们也可以把函数名替换为某个 Lua 表的某个字段，例如

```
function foo.bar(a, b, c)
    -- body ...
end
```

此时我们是把一个函数类型的值赋给了 foo 表的 bar 字段。换言之，上面的定义等价于

```
foo.bar = function (a, b, c)
    print(a, b, c)
end
```

对于此种形式的函数定义，不能再使用 local 修饰符了，因为不存在定义新的局部变量了。

#### 函数的参数

##### 按值传递

Lua 函数的参数大部分是按值传递的。值传递就是调用函数时，实参把它的值通过赋值运算传递给形参，然后形参的改变和实参就没有关系了。在这个过程中，实参是通过它在参数表中的位置与形参匹配起来的。

```
local function swap(a, b) --定义函数swap,函数内部进行交换两个变量的值
   local temp = a
   a = b
   b = temp
   print(a, b)
end

local x = "hello"
local y = 20
print(x, y)
swap(x, y)    --调用swap函数
print(x, y)   --调用swap函数后，x和y的值并没有交换

-->output
hello 20
20  hello
hello 20
```

在调用函数的时候，若形参个数和实参个数不同时，Lua 会自动调整实参个数。调整规则：若实参个数大于形参个数，从左向右，多余的实参被忽略；若实参个数小于形参个数，从左向右，没有被实参初始化的形参会被初始化为 nil。

```
local function fun1(a, b)       --两个形参，多余的实参被忽略掉
   print(a, b)
end

local function fun2(a, b, c, d) --四个形参，没有被实参初始化的形参，用nil初始化
   print(a, b, c, d)
end

local x = 1
local y = 2
local z = 3

fun1(x, y, z)         -- z被函数fun1忽略掉了，参数变成 x, y
fun2(x, y, z)         -- 后面自动加上一个nil，参数变成 x, y, z, nil

-->output
1   2
1   2   3   nil
```

##### 变长参数

上面函数的参数都是固定的，其实 Lua 还支持变长参数。若形参为 ... ,示该函数可以接收不同长度的参数。访问参数的时候也要使用 ...

```
local function func( ... )                -- 形参为 ... ,表示函数采用变长参数

   local temp = {...}                     -- 访问的时候也要使用 ...
   local ans = table.concat(temp, " ")    -- 使用 table.concat 库函数对数
                                          -- 组内容使用 " " 拼接成字符串。
   print(ans)
end

func(1, 2)        -- 传递了两个参数
func(1, 2, 3, 4)  -- 传递了四个参数

-->output
1 2

1 2 3 4
```

值得一提的是，LuaJIT 2 尚不能 JIT 编译这种变长参数的用法，只能解释执行。所以对性能敏感的代码，应当避免使用此种形式。

##### 具名参数(参数是一个 table)

Lua 还支持通过名称来指定实参，这时候要把所有的实参组织到一个 table 中，并将这个 table 作为唯一的实参传给函数。

```
local function change(arg) -- change 函数，改变长方形的长和宽，使其各增长一倍
  arg.width = arg.width * 2
  arg.height = arg.height * 2
  return arg
end

local rectangle = { width = 20, height = 15 }
print("before change:", "width  =", rectangle.width,
                        "height =", rectangle.height)
rectangle = change(rectangle)
print("after  change:", "width  =", rectangle.width,
                        "height =", rectangle.height)

-->output
before change: width = 20  height =  15
after  change: width = 40  height =  30
```

##### 按引用传递

当函数参数是 table 类型时，传递进来的是 实际参数的引用，此时在函数内部对该 table 所做的修改，会直接对调用者所传递的实际参数生效，而无需自己返回结果和让调用者进行赋值。 我们把上面改变长方形长和宽的例子修改一下。

```
function change(arg) --change函数，改变长方形的长和宽，使其各增长一倍
  arg.width = arg.width * 2  --表arg不是表rectangle的拷贝，他们是同一个表
  arg.height = arg.height * 2
end                  -- 没有return语句了

local rectangle = { width = 20, height = 15 }
print("before change:", "width = ", rectangle.width,
                        " height = ", rectangle.height)
change(rectangle)
print("after change:", "width = ", rectangle.width,
                       " height =", rectangle.height)

--> output
before change: width = 20  height = 15
after  change: width = 40  height = 30
```

在常用基本类型中，除了 table 是按址传递类型外，其它的都是按值传递参数。 用全局变量来代替函数参数的不好编程习惯应该被抵制，良好的编程习惯应该是减少全局变量的使用。

#### 函数返回值

Lua 具有一项与众不同的特性，允许函数返回多个值。Lua 的库函数中，有一些就是返回多个值。

> 示例代码：使用库函数 string.find，在源字符串中查找目标字符串，若查找成功，则返回目标字符串在源字符串中的起始位置和结束位置的下标。

```
local s, e = string.find("hello world", "llo")
print(s, e)  -->output 3  5
```

返回多个值时，值之间用 “,” 隔开。

> 示例代码：定义一个函数，实现两个变量交换值

```
local function swap(a, b)   -- 定义函数 swap，实现两个变量交换值
   return b, a              -- 按相反顺序返回变量的值
end

local x = 1
local y = 20
x, y = swap(x, y)           -- 调用 swap 函数
print(x, y)                 --> output   20     1
```

当函数返回值的个数和接收返回值的变量的个数不一致时，Lua 也会自动调整参数个数。

调整规则： 若返回值个数大于接收变量的个数，多余的返回值会被忽略掉； 若返回值个数小于参数个数，从左向右，没有被返回值初始化的变量会被初始化为 nil。

```
function init()             --init 函数 返回两个值 1 和 "lua"
  return 1, "lua"
end

x = init()
print(x)

x, y, z = init()
print(x, y, z)

--output
1
1 lua nil
```

当一个函数有一个以上返回值，且函数调用不是一个列表表达式的最后一个元素，那么函数调用只会产生一个返回值,也就是第一个返回值。

```
local function init()       -- init 函数 返回两个值 1 和 "lua"
    return 1, "lua"
end

local x, y, z = init(), 2   -- init 函数的位置不在最后，此时只返回 1
print(x, y, z)              -->output  1  2  nil

local a, b, c = 2, init()   -- init 函数的位置在最后，此时返回 1 和 "lua"
print(a, b, c)              -->output  2  1  lua
```

函数调用的实参列表也是一个列表表达式。考虑下面的例子：

```
local function init()
    return 1, "lua"
end

print(init(), 2)   -->output  1  2
print(2, init())   -->output  2  1  lua

```

如果你确保只取函数返回值的第一个值，可以使用括号运算符，例如

```
local function init()
    return 1, "lua"
end

print((init()), 2)   -->output  1  2
print(2, (init()))   -->output  2  1
```

值得一提的是，如果实参列表中某个函数会返回多个值，同时调用者又没有显式地使用括号运算符来筛选和过滤，则这样的表达式是不能被 LuaJIT 2 所 JIT 编译的，而只能被解释执行。

#### 全动态函数调用

调用回调函数，并把一个数组参数作为回调函数的参数。

```
local args = {...} or {}
method_name(unpack(args, 1, table.maxn(args)))
```

##### 使用场景

如果你的实参 table 中确定没有 nil 空洞，则可以简化为

```
method_name(unpack(args))
```

1. 你要调用的函数参数是未知的；
2. 函数的实际参数的类型和数目也都是未知的。

```
add_task(end_time, callback, params)

if os.time() >= endTime then
    callback(unpack(params, 1, table.maxn(params)))
end
```

值得一提的是，unpack 内建函数还不能为 LuaJIT 所 JIT 编译，因此这种用法总是会被解释执行。对性能敏感的代码路径应避免这种用法。

##### 小试牛刀

```
local function run(x, y)
    print('run', x, y)
end

local function attack(targetId)
    print('targetId', targetId)
end

local function do_action(method, ...)
    local args = {...} or {}
    method(unpack(args, 1, table.maxn(args)))
end

do_action(run, 1, 2)         -- output: run 1 2
do_action(attack, 1111)      -- output: targetId    1111
```

来源
[https://moonbingbing.gitbooks.io/openresty-best-practices/content/lua/class.html](https://moonbingbing.gitbooks.io/openresty-best-practices/content/lua/class.html)
