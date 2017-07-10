---
title: '好耶~Rust,PHP,Lua,Python,C++,Java性能大比拼'
date: 2017-07-10 14:23:46
tags: 程序设计 评测 跑分 程序设计语言 性能
---
duangsuse突然就想做一个这种评测>_<今天总算有时间了

### 评测选择
1. 不比试编译速度
2. 预编译语言统一开优化 ~~(本来计划不开了,但是我嫌麻烦)~~
3. 其它什么特性啊,语言多优雅简练啊,编译速度快什么的都无视,只看速度 
4. 统一管道time记时,如果Broken Pipe就先导向到/dev/null
5. 我不会尝试检验程序运行的结果是否正确,,,~~比如数学计算有没有错误~~,,,,

#### 评测语言
编译性 `Rust` `C++` `Java` `Go` `Scheme(只有termio和math部分)` `ECMA Script`
解释性 `Python(3)` `PHP` `Lua` 
>注:Scheme使用__Chez Scheme__作为编译器,C++使用__GCC__作为编译器,ECMA Script(javascript)以__node__作为解释器 

#### 评测环境
操作系统: `Arch Linux` 由 `x86_64` Linux 4.9.33-1-lily 内核驱动
Shell: `zsh` 5.3.1

##### CPU
架构：           x86_64
CPU 运行模式：   32-bit, 64-bit
字节序：         Little Endian
CPU:             2
在线 CPU 列表：  0,1
每个核的线程数： 1
每个座的核数：   2
座：             1
NUMA 节点：      1
厂商 ID：        GenuineIntel
CPU 系列：       6
型号：           42
型号名称：       Intel(R) Celeron(R) CPU 847 @ *1.10GHz*
步进：           7
CPU MHz：        799.957
CPU 最大 MHz：   1100.0000
CPU 最小 MHz：   800.0000
BogoMIPS：       2195.42
虚拟化：         VT-x
L1d 缓存：       32K
L1i 缓存：       32K
L2 缓存：        256K
L3 缓存：        2048K
NUMA 节点0 CPU： 0,1
标记：  *
##### RAM
RANGE                                  SIZE  STATE REMOVABLE BLOCK
0x0000000000000000-0x0000000077ffffff  1.9G online        否  0-14
0x0000000078000000-0x000000007fffffff  128M online        是    15
0x0000000100000000-0x0000000107ffffff  128M online        否    32

Memory block size:       128M
Total online memory:     *2.1G*
Total offline memory:      0B
#### 测试项目
+ termio stdio速度
+ fileio 文件io速度
+ math 数学+-*/计算 ~~(duangsuse倒数)~~ 的速度
+ stringreplace 字符串替换的速度

### ::评测1 stdio读写速度

#### 程序的代码
> ~~PHP,C++,Scheme,ECMA的代码都是网上抄的~~
> Scheme,ECMA的测评以后再...>_>

##### Rust
```rust
fn main() {
	for _ in 0..1000 {
		println!("Hello,world!");
	}
}
```
##### Python
```python
for i in range(0, 1000):
    print("Hello,world!")
```
##### Java
```java
class main {
	public static void main(String[] args) {
		for (int i=0;i<1000;i++) {
			System.out.println("Hello,world!");
	    }
	}
}
```
##### Lua
```lua
i=0
while i<1000 do
	print("Hello,world!")
	i=i+1
end
```
##### PHP
```php
<?php for ($i=0;$i<1000;$i++) {
	echo "Hello,world!\n";
}
?>
```
##### Go
```go
package main

import "fmt"
func main() {
	var i=0;
	for i<1000 {
		fmt.Printf("Hello,world!\n")
		i++
	}
}
```
##### C++
```c++
#include <stdio.h>
int main()
{
		for (int i=0;i<1000;i++) {
   		printf("Hello, World!\n");
		}
		return 0;
}
```


#### 测(已和谐一部分警告
P1:
```plain
➜  termio gcc -O2 cpp.cpp 
➜  termio rustc -O rust.rs
➜  termio luac lua.lua 
➜  termio javac java.java 
➜  termio ./a.out|time #C++
shell  2.79s user 0.62s system 0% cpu 41:06.08 total
children  77.36s user 11.18s system 3% cpu 41:06.08 total
➜  termio ./rust|time #Rust
shell  2.86s user 0.62s system 0% cpu 41:17.02 total
children  77.37s user 11.18s system 3% cpu 41:17.02 total
thread 'main' panicked at 'failed printing to stdout: Broken pipe (os error 32)', /checkout/src/libstd/io/stdio.rs:692:8
note: Run with `RUST_BACKTRACE=1` for a backtrace.
➜  termio ./rust >/dev/null|time #Rust
shell  2.95s user 0.64s system 0% cpu 41:55.02 total
children  77.37s user 11.18s system 3% cpu 41:55.02 total
➜  termio go build go.go 
➜  termio ./go|time
shell  3.04s user 0.65s system 0% cpu 42:37.78 total
children  77.83s user 11.35s system 3% cpu 42:37.78 total
➜  termio lua luac.out|time
shell  3.07s user 0.66s system 0% cpu 42:57.80 total
children  77.84s user 11.35s system 3% cpu 42:57.80 total
➜  termio lua lua.lua|time 
shell  3.10s user 0.67s system 0% cpu 43:03.19 total
children  77.85s user 11.37s system 3% cpu 43:03.19 total
➜  termio python python.py >/dev/null |time
shell  3.23s user 0.68s system 0% cpu 43:25.10 total
children  77.86s user 11.37s system 3% cpu 43:25.10 total
➜  termio php php.php|time
shell  3.24s user 0.69s system 0% cpu 43:32.12 total
children  77.93s user 11.38s system 3% cpu 43:32.12 total
➜  termio java main|time
shell  3.26s user 0.70s system 0% cpu 43:42.68 total
children  77.97s user 11.43s system 3% cpu 43:42.68 total
```
分数如下:

语言|分数|可执行文件大小(可能影响启动速度)
:--|:--|:--
Java|3.26s|461b
Python|3.23s|Nil
C++|2.79s|8.2kb
Rust|2.95s|3.9MiB吐血(可以再优化或nostd
Go|~~1s~~好吧是3.04s,我不确定优化情况.请评论|1.6MiB(
Lua|3.01s|Nil
Lua(PreCompileChunk)|3.07s|236b
PHP|3.24s|Nil

那么我想说什么呢?:
1. Java表现居然比Lua差(dawu
2. PHP就比Java快算什么最吼的语言
3. Rust这个bin大小很是问题?不过幸而速度较快所以~~即使体积不止1000倍于C++还是只差....不到1s :-O
4.  你们搞得这个Go啊 ,Excited!~~居然我Lua都没比过,是没开优化吗?~~

### ::评测二 :文件io

#### 大文件预备
`dd if=/dev/urandom of=test`
~~然后我随便掐个时间停止,没毛病~~
>为保证比赛~~赛艇~~公平我特意先让随机语言写的测试程序读取了文件,保证文件此时已在Buffer中
~~如果后来缓存被清空了呢?XD~~

#### 代码

##### Rust
```rust
use std::io::prelude::*;
use std::fs::File;

#[allow(unused_must_use)]
fn main() {
	if let Ok(mut f) = File::open("test") {
		let mut file = String::new();
		f.read_to_string(&mut file); //好像比python多了一层转码??? 没有吧?
		println!("OK");
	}
}
```
##### Python
```python
import string
f=open('test','r')
s=f.buffer.read()#.decode() 小莽没办法正常解码,我那就不是utf8
f.close()
```
##### Java
```java
import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
import java.io.IOException;
public class ReadFile {
 public static void main(String[] args) throws IOException {
  String fileContent = readFileContent("test");
  
  //System.out.println(fileContent);
 }
             //参数string为你的文件名 来源某度知道
 private static String readFileContent(String fileName) throws IOException {
  File file = new File(fileName);
  
  BufferedReader bf = new BufferedReader(new FileReader(file));
  
  String content = "";
  StringBuilder sb = new StringBuilder();
  
  while(content != null){
   content = bf.readLine();
   
   if(content == null){
    break;
   }
   
   sb.append(content.trim());
  }
  
bf.close();
  return sb.toString();
 }
}

```
##### Lua
>注:Lua无法解析字符链接,害得我把文件移动了才行
>注:以上瞎扯,是我自己没加*all

```lua
f=io.open("test",'r')
s=f:read('*all')
f.close()
```
##### PHP
```plain
这个就算了>_>
不要让PHP丢脸
```
##### Go
>来源Geek学院

```go
package main
import (
    "bufio"
    "fmt"
    "io"
    "os"
)

func main() {
    inputFile, inputError := os.Open("test")
    if inputError != nil {
        fmt.Printf("An error occurred on opening the inputfile\n" +
            "Does the file exist?\n" +
            "Have you got acces to it?\n")
        return // exit the function on error
    }
    defer inputFile.Close()

    inputReader := bufio.NewReader(inputFile)
    for {
        /*inputString*/ _ , readerError := inputReader.ReadString('\n')
    //fmt.Printf("The input was: %s", inputString)
        if readerError == io.EOF {
            return
        }      
    }
}
```
##### C++
>来源请求?

```c++
#include <iostream>
#include <fstream>
using namespace std;

void main()
{
char buffer[256];
fstream out;
out.open("com.txt",ios::in);
cout<<"com.txt"<<" 的内容如下:"<<endl;
while(!out.eof())
{
   out.getline(buffer,256,'\n');//getline(char *,int,char) 表示该行字符达到256个或遇到换行就结束
   //cout<<buffer<<endl; 200MiB大文件
}
out.close();
cin.get();//cin.get() 是用来读取回车键的,如果没这一行，输出的结果一闪就消失了
}
```

#### 测试
```plain
➜  termio dd if=/dev/urandom of=test
^C记录了379638+0 的读入
记录了379638+0 的写出
194374656 bytes (194 MB, 185 MiB) copied, 6.01315 s, 32.3 MB/s

➜  fileio lua lua.lua|time
shell  1.43s user 0.42s system 0% cpu 3:27:00.93 total
children  22.76s user 8.36s system 0% cpu 3:27:00.93 total
注:java由于是分开的jvm进程,无法正常计时(之前的数据不知道正不正确,但它已经退出游戏了...
(笑话,因为这个本来就是不能解码的
➜  fileio java -Xms400m ReadFile|time
shell  2.01s user 0.53s system 0% cpu 3:32:18.23 total
children  75.67s user 11.40s system 0% cpu 3:32:18.23 total
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at java.util.Arrays.copyOf(Arrays.java:3332)
	at java.lang.AbstractStringBuilder.ensureCapacityInternal(AbstractStringBuilder.java:124)
	at java.lang.AbstractStringBuilder.append(AbstractStringBuilder.java:448)
	at java.lang.StringBuilder.append(StringBuilder.java:136)
	at ReadFile.readFileContent(ReadFile.java:27)
	at ReadFile.main(ReadFile.java:7)

➜  fileio ./go|time
shell  2.42s user 0.60s system 0% cpu 3:39:25.09 total
children  92.70s user 12.40s system 0% cpu 3:39:25.09 total

➜  fileio rustc -O rust.rs 
➜  fileio ./rust 
OK
➜  fileio ./rust|time
shell  2.55s user 0.63s system 0% cpu 3:40:21.96 total
children  95.52s user 13.18s system 0% cpu 3:40:21.96 total
➜  fileio 

➜  fileio python python.py >/dev/null |time
shell  2.76s user 0.68s system 0% cpu 3:41:12.58 total
children  95.69s user 13.57s system 0% cpu 3:41:12.58 total

➜  fileio gcc -O2 cpp.cpp
/tmp/ccVDUZyA.o：在函数‘main’中：
cpp.cpp:(.text.startup+0x11)：对‘std::basic_fstream<char, std::char_traits<char> >::basic_fstream()’未定义的引用
cpp.cpp:(.text.startup+0x28)：对‘std::basic_fstream<char, std::char_traits<char> >::open(char const*, std::_Ios_Openmode)’未定义的引用
cpp.cpp:(.text.startup+0x37)：对‘std::cout’未定义的引用
.........balabala.......
不想玩了...
```
结果:

语言|速度|二进大小|
:--|:--|:--
Rust|2.55s|3.9M
C++|例子链接时失败|没有生成
Python|2.76s|Nil
Lua|1.43s|Nil
Java|*至少2s*|1K
PHP|没参加|Nil
Go|2.42s|1.8M

我想说>_>
大家参考一下吧,Lua这个分不正常
Rust可能居然额外做了解码的工作,可能不公平

### ::测试3: 数学运算
\>_< 让他们算 3+4
然后*100 再除 5000 再减去200
算10000遍
expr:(3+4)*100/500-200
代码我就不放了
基于Helloworld循环改吧.
#### 测试
Lua: shell  5.22s user 1.11s system 0% cpu 4:01:30.61 total
children  90.09s user 18.41s system 0% cpu 4:01:30.61 total

Java:shell  5.35s user 1.12s system 0% cpu 4:01:58.74 total
children  90.33s user 18.46s system 0% cpu 4:01:58.74 total

Python:shell  5.47s user 1.14s system 0% cpu 4:02:22.24 total
children  90.68s user 18.50s system 0% cpu 4:02:22.24 total

Go:shell  5.58s user 1.15s system 0% cpu 4:02:39.55 total
children  90.77s user 18.52s system 0% cpu 4:02:39.55 total

Rust:SystemTime { tv_sec: 1499683171, tv_nsec: 66820478 }
SystemTime { tv_sec: 1499683214, tv_nsec: 125282928 }
shell  6.51s user 1.31s system 0% cpu 4:14:14.37 total
children  96.95s user 19.62s system 0% cpu 4:14:14.37 total

>注:Rust会因重定向而panic,不得不用内部方法:后来去掉了println!的部分

PHP:shell  6.26s user 1.27s system 0% cpu 4:12:45.00 total
children  95.78s user 19.40s system 0% cpu 4:12:45.00 total

C++:shell  6.36s user 1.30s system 0% cpu 4:13:00.75 total
children  95.80s user 19.43s system 0% cpu 4:13:00.75 total

\>_> 这个....可以有
肯定不对,,,Java比C快

### 到此为止了

本来想正常测试完的 >_<
但是令人奇怪的事情越变越多了呢?
至于下部等到我拿到新电脑以后再`重新测试`,届时会采用`内部计时`的方法,希望能减小误差供大家参考 :_(
