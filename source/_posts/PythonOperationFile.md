---
title: Python文件操作
date: 2017-5-02 13:55:01
categories: Python
tags:
- IO
- 文件操作
---

![}DYRSSIVPJ4V8SC_ILPU41](PythonOperationFile\}DYRSSIVPJ4V8SC_ILP[U41.jpg)

## 前言

读写文件是最常见的IO操作。Python内置了读写文件的函数，用法和C是兼容的。

读写文件前，我们先必须了解一下，在磁盘上读写文件的功能都是由操作系统提供的，现代操作系统不允许普通的程序直接操作磁盘，所以，读写文件就是请求操作系统打开一个文件对象（通常称为文件描述符），然后，通过操作系统提供的接口从这个文件对象中读取数据（读文件），或者把数据写入这个文件对象（写文件）。

<!--MORE-->

函数 open() 返回文件对象，通常的用法需要两个参数：open(filename, mode)。

```
f = open('/tool/file', 'w')
```

第一个参数是一个标识文件名的字符串。第二个参数是由有限的字母组成的字符串，描述了文件将会被如何使用。可选的 *模式* 有： 'r'，此选项使文件只读； 'w' ，此选项使文件只写（对于同名文件，该操作使原有文件被覆盖）； 'a' ，此选项以追加方式打开文件； 'r+' ，此选项以读写方式打开文件； 模式 参数是可选的。如果没有指定，默认为 'r' 模式。

在 Windows 平台上， 'b' 模式以二进制方式打开文件，所以可能会有类似于 'rb' ， 'wb' ，'r+b' 等等模式组合。Windows 平台上文本文件与二进制文件是有区别的，读写文本文件时，行尾会自动添加行结束符。这种后台操作方式对 ASCII 文本文件没有什么问题，但是操作 JPEG 或 EXE这样的二进制文件时就会产生破坏。在操作这些文件时一定要记得以二进制模式打开。在 Unix 上，加一个 'b' 模式也一样是无害的，所以你可以一切二进制文件处理中平台无关的使用它。

- 读文件
- 写文件
- 读取多个文件

## 读文件

```
# 读取txt文件每行数据并返回list列表
def readtxt(path):
    import re

    # 你的文件路径
    # path = "makedata_input.txt"
    # 读取文件
    file = open(path, encoding="utf-8")
    # 定义一个用于切割字符串的正则
    seq = re.compile("\s+")

    result = []
    # 逐行读取
    for line in file:
        result.append(line[2:-1])

    # 关闭文件
    file.close()
    return result
```

## 写文件

函数 open() 返回文件对象，通常的用法需要两个参数：open(filename, mode)。

```
# 把list数据根据分组信息输出到txt文件中
def writetxt(result_list):
    file1 = codecs.open('data1.txt', 'w', 'utf-8')
    file1.write(str(result_list));
    file1.close()
```

## 读取多个文件

模块os中的walk()函数可以遍历文件夹下所有的文件。

该函数可以得到一个三元tupple(dirpath, dirnames, filenames).

该函数可以得到一个三元tupple(dirpath, dirnames, filenames).参数含义：dirpath：string，代表目录的路径；dirnames：list，包含了当前-	---

- dirpath：string，代表目录的路径；
- dirnames：list，包含了当前dirpath路径下所有的子目录名字（不包含目录路径）；
- filenames：list，包含了当前dirpath路径下所有的非目录子文件的名字（不包含目录路径）。

注意，dirnames和filenames均不包含路径信息，如需完整路径，可使用os.path.join(dirpath, dirnames)

```
import os
#Get file name from path
def file_name(file_dir):
    for root, dirs, files in os.walk(file_dir):
        # print(root)  # 当前目录路径
        # print(dirs)  # 当前路径下所有子目录
        # print(files)  # 当前路径下所有非目录子文件
        return files
path_list = file_name(INPUT_FILE_NAME_PATH)#文件夹下所有文件名称
syslen = len(path_list)
in_lists = [] # 每个文件内容存放一个list
for i in range(0, syslen):
    with open(path_list[i], 'r', encoding='utf-8') as in_file:
        in_list = in_file.readlines()  # for line in in_list:
    in_lists.append(in_list)
```