---
title: 一文解决编码问题
date: 2020-02-14 23:30:34
categories:
- 编程
tags:
- 编码
- python

---
## 一、字符编码简单介绍
在计算机内部，全部的信息终于都表示为一个二进制的字符串。每个二进制位（bit）有0和1两种状态，因此八个二进制位就能够组合出256种状态，这被称为一个字节（byte）。也就是说，一个字节一共能够用来表示256种不同的状态，每个状态相应一个符号，就是256个符号，从0000000到11111111。一个字节能表示的最大的整数就是255（二进制11111111=十进制255），如果要表示更大的整数，就必须用更多的字节。比如两个字节可以表示的最大整数是`65535`，4个字节可以表示的最大整数是`4294967295`。
### 1. ASCII码

上个世纪60年代，美国制定了一套字符编码，对英语字符与二进制位之间的关系，做了统一规定。这被称为ASCII码，一直沿用至今。
ASCII码一共规定了128个字符的编码，比方空格“SPACE”是32（二进制00100000），大写的字母A是65（二进制01000001）。这128个符号（包含32个不能打印出来的控制符号），仅仅占用了一个字节的后面7位，最前面的1位统一规定为0。

### 2. GB2312
计算机进入中国后，无法显示中文，一个自己已经被占满了，我国重新制定了一个编码表，将扩展的第八位对应的拉丁文全部删掉，规定一个小于127的字符与原来的意义相同，当两个大于127的字符链接在一起的时候，就表示一个汉字，前面一个字节为高字节（0xA1-0xF7），后面一个字节为低字节(0xA1-0xFE)，这样可以表示7000多个汉字，这种编码叫做GB2312。GB2312是对ASCII的中文扩展

### 3. GBK和GB18030
由于汉字的数量太大，GB2312不能满足需求，后来规定只要第一个字节大于127就固定表示一个汉字，不管后面的是不是扩展字符集里面的内容，扩展后的编码成为GBK, GBK包括了GB2312的所有内容，同时增加了近20000个新的汉字（包括繁体）和符号
### 4. Unicode
至于亚洲国家的文字，使用的符号就很多其它了，汉字就多达10万左右。一个字节仅仅能表示256种符号，肯定是不够的，就必须使用多个字节表达一个符号。比方，中文简体常见的编码方式是GB2312，使用两个字节表示一个汉字，所以理论上最多能够表示256x256=65536个符号。

世界上存在着多种编码方式，同一个二进制数字能够被解释成不同的符号。因此，要想打开一个文本文件，就必须知道它的编码方式，否则用错误的编码方式解读，就会出现乱码。为什么电子邮件经常出现乱码？就是由于发信人和收信人使用的编码方式不一样。
能够想象，假设有一种编码，将世界上全部的符号都纳入当中。每个符号都给予一个独一无二的编码，那么乱码问题就会消失。这就是Unicode，就像它的名字都表示的，这是一种全部符号的编码。

Unicode当然是一个非常大的集合，如今的规模能够容纳100多万个符号。每一个符号的编码都不一样。须要注意的是，**Unicode仅仅是一个符号集，它仅仅规定了符号的二进制代码，却没有规定这个二进制代码应该怎样存储。**
### 3. UTF-8
互联网的普及，强烈要求出现一种统一的编码方式。UTF-8就是在互联网上使用最广的一种unicode的实现方式。其它实现方式还包含UTF-16和UTF-32，只是在互联网上基本不用。反复一遍，这里的关系是，UTF-8是Unicode的实现方式之中的一个。
UTF-8最大的一个特点，就是它是一种变长的编码方式。它能够使用1~6个字节表示一个符号，依据不同的符号而变化字节长度。
UTF-8的编码规则非常easy，仅仅有二条：
1）对于单字节的符号，字节的第一位设为0，后面7位为这个符号的unicode码。因此对于英语字母，UTF-8编码和ASCII码是同样的。
2）对于n字节的符号（n>1），第一个字节的前n位都设为1，第n+1位设为0，后面字节的前两位一律设为10。剩下的没有提及的二进制位，所有为这个符号的unicode码。
如表： 
1字节 0xxxxxxx 
2字节 110xxxxx 10xxxxxx 
3字节 1110xxxx 10xxxxxx 10xxxxxx 
4字节 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx 
5字节 111110xx 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx 
6字节 1111110x 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx 
因此UTF-8中能够用来表示字符编码的实际位数最多有31位，即上表中x所表示的位。除去那些控制位（每字节开头的10等），这些x表示的位与UNICODE编码是一一相应的，位高低顺序也同样。 
实际将UNICODE转换为UTF-8编码时应先去除高位0，然后依据所剩编码的位数决定所需最小的UTF-8编码位数。 
因此那些基本ASCII字符集中的字符（UNICODE兼容ASCII）仅仅须要一个字节的UTF-8编码（7个二进制位）便能够表示。 
## 常用操作

[参考](https://linux.cn/article-7959-1.html"%3Ehttps://linux.cn/article-7959-1.html)

```bash
iconv -f utf-8 -t gb2312 aaa.txt >bbb.txt   # 转换
:set fileencoding=utf-8 # vim 转换
file your_file.txt # 查看编码类型
:set fileencoding # vim 查看编码类型
```


## Python 中的编码
[参考](https://blog.csdn.net/danlei94/article/details/77116984) [参考](https://www.jb51.net/article/159713.htm)

如果使用python3基本避免了编码问题，所以新手别用py2了，浪费太多时间在上面。
从四个场景和常见问题进行介绍，同时强烈建议如果不是特殊需求，文件保存的方式都是utf8
#### 代码文件中的字符
代码文件中的字符就是 `main.py` 这种py文件中的字符，下面有py2 和py3 的示例。

``` python
#encoding=utf8
# -*- coding: utf-8 -*-
# 上面两种都行选一个就好，一定要写，这个是告诉py解释器文件保存方式。
# 同时保证文件的存储一定是按照utf8存储。Windows可以用sublime/notepad++存储为UTF8 without BOM这种方式，Linux天然UTF8

s1= '中文str'
s2=u'中文str'
print(s1.__class__, s2.__class__, s1, s2)

#py2 output
(<type 'str'>, <type 'unicode'>)
中文str
中文str
#py3 output
<class 'str'> <class 'str'> 
中文str 
中文str
```
由此可见py3中已经把str和Unicode统一起来了，但是在py2中还进行了区分
#### I/O字符
```python

#py2
# Python2 里面编码是 unicode -> str，相反的，解码就是 str -> unicode。
# 若str对象调用encode会默认先按系统默认编码方式 decode成unicode对象再encode，忽视了中间默认的decode往往导致报错。 
# 我们没有指明解码方式，python 就会使用 sys.defaultencoding 指明的方式来解码。很多情况下 sys.defaultencoding为ANSCII，如果 str 不是这个类型就会出错。
# 输出时候设置setdefaultencoding来解决py2打印不正确的问题

#encoding=utf8
import sys
reload(sys)
sys.setdefaultencoding("utf-8")
print sys.getdefaultencoding()

import codecs
file = 'a.in' # content: 中文字符str进行测试
f = codecs.open(file,"r","utf-8") # 利用codecs直接转成unicode进行处理
fwGbk = open(file + '.gbk', 'w') 
fwUtf8 = open(file + '.utf8', 'w') 
fwDefault = open(file + '.default', 'w') #文件的读写操作默认使用系统编码，可以通过调用 sys.getdefaultencoding() 来得到
for line in f:
    print(type(line), line)
    fwUtf8.write(line.encode("utf-8"))
    fwGbk.write(line.encode("gb2312"))
    fwDefault.write(line))

# ==============================================================
#py3
f = open(file, 'r', encoding='utf8') # 如果是gbk 有时候范围不够用 open(‘1.txt’,encoding=’gb18030’，errors=‘ignore’)；
fwGbk = open(file + '.gbk', 'w', encoding='gbk')
fwUtf8 = open(file + '.utf8', 'w', encoding='utf8')
fwDefault = open(file + '.default', 'w')
for line in f:
    fwGbk.write(gbk)
    fwUtf8.write(utf8)
    fwDefault.write(line)
```


#### 常见问题
1. unicodeencodeerror: 'ascii' codec can't encode characters
因为python2默认是ASCII编码str，所以对于读取信息str为utf8的就死掉了。

2. UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-3: ordinal not in range(128)
在进行同时包含 str 与 unicode 的运算时，Python 一律都把 str 转换成 unicode 再运算，当然，运算结果也都是 unicode。由于 Python 事先并不知道 str 的编码，它只能使用 sys.getdefaultencoding() 编码去 decode。在我的印象里，sys.getdefaultencoding() 的值总是 'ascii' ，如果需要转换的 str 有中文，一定会出现错误。
3. 打印出的结果还是有问题。如果是用终端查看的，确保xshell/Xterm等程序编码设置正确
## C++ 中的编码
[参考](https://blog.csdn.net/netyeaxi/article/details/81036482)  [参考](https://blog.csdn.net/calmreason/article/details/7935258)
C++ 用着更难受，因为偏底层的原因，各种转换申请/释放空间都繁琐，最不爱用了。

```C++
// C++标准库中对应关系：

char *        std::string
wchar_t*   std::wstring
char16_t*  std::u16string
char32_t*  std::u32string
// C++标准库从C++11开始提供了std::codecvt_utf8和std::codecvt_byname两个转换器来完成编码转换，可使用的通用代码如下


#include <string>
#include <locale>
#include <codecvt>
 
// string的编码方式为utf8，则采用：
std::string wstring2utf8string(const std::wstring& str)
{
    static std::wstring_convert<std::codecvt_utf8<wchar_t> > strCnv;
    return strCnv.to_bytes(str);
}
 
std::wstring utf8string2wstring(const std::string& str)
{
    static std::wstring_convert< std::codecvt_utf8<wchar_t> > strCnv;
    return strCnv.from_bytes(str);
}
 
// string的编码方式为除utf8外的其它编码方式，可采用：
std::string wstring2string(const std::wstring& str, const std::string& locale)
{
    typedef std::codecvt_byname<wchar_t, char, std::mbstate_t> F;
    static std::wstring_convert<F> strCnv(new F(locale));
 
    return strCnv.to_bytes(str);
}
 
std::wstring string2wstring(const std::string& str, const std::string& locale)
{
    typedef std::codecvt_byname<wchar_t, char, std::mbstate_t> F;
    static std::wstring_convert<F> strCnv(new F(locale));
 
    return strCnv.from_bytes(str);
}

如果是GBK string与wstring互相转化，locale可取值：
linux下：
zh_CN.GBK
zh_CN.GB2312
zh_CN.GB18030

windows下：
标准格式的locale：
Chinese_China.936
zh-CN
.936
```











