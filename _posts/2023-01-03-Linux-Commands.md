---
title: 常用 Linux 指令合辑
date: 2023-01-03 22:11:00 +0800
categories: [Backend]
tags: [Linux, Command]
render_with_liquid: false
---

练习命令行的[网站](https://cmdchallenge.com/)

# 1 pwd (print working directory)

给出现行目录

# 2 ls (list)

用的比较多，在这里记录一些之前不知道的用法

- **ls -R** : 若目录下有文件，则以下之文件亦皆依序列出
- **ls -a** : 显示隐藏文件
- **ls -l** : 除文件名称外，将文件型态、权限、拥有者、文件大小等资讯详细列出
- **ls -lh**: 跟-l的格式差不多，但是文件大小表示方法用B、K、G等表示，更加清晰

<img src="https://user-images.githubusercontent.com/84035000/210376176-cec061a1-abba-473f-bc7a-221f6abf2ecf.png" alt="image" style="zoom:50%;" />

# 3 cat (concatenate)

用于连接文件并打印到标准输出设备上，参数说明：

- -n or --number: 从1开始对所有的行数编号
- -b or --number-nonblank: 同样是编号，只是对空白行不编号
- -s or --squeeze-blank: 当遇到有连续两行以上的空白行，就代换为一行的空白行
- -E or --show-ends: 在每行结束处显示 $

用法举例：

|             指令              |                        含义                         |
| :---------------------------: | :-------------------------------------------------: |
|         cat > xxx.txt         |               新建了一个xxx.txt的文件               |
| cat aaa.txt bbb.txt > ccc.txt | 合并aaa.txt和bbb.txt并新建一个ccc.txt来存储合并结果 |
| cat /dev/null > /etc/test.txt |             清空 /etc/test.txt 文档内容             |



# 4 touch

用于修改文件或者目录的时间属性，包括存取时间和更改时间。若文件不存在，系统会建立一个新的文件。

用法举例：

|             指令              |          含义           |
| :---------------------------: | :---------------------: |
|         touch xxx.txt         | 新建了一个xxx.txt的文件 |
| touch aa1.txt aa2.txt aa3.txt |   同时新建了三个文件    |
|      touch aa{1..3}.txt       |       和上面一样        |

# 5 locate

用于查找符合条件的文档，机器接受指令后会去保存文档和目录名称的数据库内查找合乎范本样式条件的文档或目录。

参数：

- -c or --count: 只输出找到的数量，用于查看某目录下文件数目
- -i or --ignore-cast: 忽略大小写

用法举例：

|        指令         |                      含义                       |
| :-----------------: | :---------------------------------------------: |
| locate the_file.abc |          精准查找the_file.abc这一文件           |
|   locate /etc/sh    |       搜索 etc 目录下所有以 sh 开头的文件       |
|    locate -i ~/r    | 忽略大小写搜索当前用户目录下所有以 r 开头的文件 |

P.S.

- locate 与 find 不同，find 是去硬盘找，locate 只在 /var/lib/slocate 资料库中找

- locate 的速度比 find 快，它并不是真的查找，而是查数据库，一般文件数据库在 /var/lib/slocate/slocate.db 中，所以 locate 的查找并不是实时的，而是以数据库的更新为准，一般是系统自己维护，也可以手工升级数据库 ，命令为：

  ```bash
  updatedb
  ```

  默认情况下 updatedb 每天执行一次。



# 6 find

在指定目录下查找文件，任何位于<u>参数之前的字符串都将被视为欲查找的目录名</u>。如果使用该命令时不设置任何参数，则 find 命令将在当前目录下查找子目录与文件。并且将查找到的子目录和文件全部进行显示。

参数：

- -empty : 空文件
- -name：文件名称符合 name 的文件
- -iname：文件名称符合 name 的文件，且不分大小写
- -type x：文件类型是 x 的文件，其中文件类型有：
  - d: 目录
  - f: 一般文件
- -amin n : 在过去 n 分钟内被读取过
- -atime n : 在过去 n 天内被读取过的文件
- -cmin n : 在过去 n 分钟内被修改过
- -ctime n : 在过去 n 天内创建的文件
- -mtime n : 在过去 n 天内修改过的文件

用法举例：

|          指令           |                           含义                           |
| :---------------------: | :------------------------------------------------------: |
|    find . -name *.c     | 将当前目录及其子目录下所有文件后缀为 **.c** 的文件列出来 |
|    find . -ctime  20    |  将当前目录及其子目录下所有最近 20 天内更新过的文件列出  |
| find . -type d -name ww |                查找当前目录下叫ww的文件夹                |

# 7 grep (global regular expression(RE) print)

用于查找文件里符合条件的字符串

参数：

- **-A** – print lines before the matched string.
- **-B** – print lines after the matched string.
- **-C** – print lines before and after the matched string.

![ABC regular expression pattern for the grep command](https://www.hostinger.com/tutorials/wp-content/uploads/sites/2/2019/02/Grep-ABC-flags.webp)

- **-i** – case insensitive search.
- **-w** – searches for full words only, ignoring your string if it’s a part of another word.
- **-c** – will show the number of matches with the searched pattern.
- **-r** – enables recursive search in the current directory.
- **-n** – 在显示符合样式的那一行之前，标示出该行的列数编号

用法举例：

|                             指令                             |                          含义                          |
| :----------------------------------------------------------: | :----------------------------------------------------: |
|                    grep VPS Hostinger.txt                    |             在Hostinger.txt找所有带VPS的行             |
|                    grep VPS a.text b.txt                     |          同时在a.txt和b.txt中找所有带VPS的行           |
|                          grep VPS *                          |    在当前目录所有文件中找带有VPS的行【搜多个文件】     |
| grep 'query1\|query2' file <br />grep -e query1 -e query2 file <br />grep -E 'query1\|query2' file | 在file中找同时有query1和2的行<br />【同时搜多个query】 |
|                        grep '^H.*o$'                         |    寻找以H开头，以o结尾的行【^限定开头，$限定结尾】    |
|               grep query file > OutputFile.txt               |           把grep的结果存在OutputFIle.txt里面           |



# 8 df (disk free) & du (disk usage)

## $ df

用于显示目前在 Linux **<u>系统上的文件系统磁盘</u>**使用情况统计。

一般使用

- df -h : 用人类可读的格式

## $ du

显示**<u>指定的目录或文件</u>**所占用的磁盘空间。

参数：

- h or --human-readable : 以K，M，G为单位，提高信息的可读性
- -s or --summarize : 仅显示总计

# 9 head & tail

## $ head

用于查看文本的前10行，当然也可以通过参数指定行数

参数：

- -q : 隐藏文件名
- -v : 显示文件名
- -c : <数目> 显示的字节数
- -n : <行数> 显示的行数

## $ tail 

用于查看文本的最后10行，用法与head基本一致



# 10 chomd (change mode)

控制用户对文件的权限

Linux/Unix 的文件调用权限分为三级 : 文件所有者（Owner）、用户组（Group）、其它用户（Other Users）。

<img src="https://www.runoob.com/wp-content/uploads/2014/08/file-permissions-rwx.jpg" alt="img" style="zoom:50%;" />

只有文件所有者和超级用户可以修改文件或目录的权限。

可以使用绝对模式（八进制数字模式），符号模式指定文件的权限。

<img src="https://www.runoob.com/wp-content/uploads/2014/08/rwx-standard-unix-permission-bits.png" alt="img" style="zoom:75%;" />

## # 符号模式

example：

```bash
chmod ugo+r file1.txt  # 将文件 file1.txt 设为所有人皆可读取 
chmod a+r file1.txt    # 将文件 file1.txt 设为所有人皆可读取，同上
chmod ug+w,o-w file1.txt file2.txt  # 将俩文件设置为user和group可写，其余人不可写
chmod u+x ex1.py       # 为 ex1.py 文件拥有者增加可执行权限
```

其中：

- u代表user，文件所有者
- g代表group，文件所有者所在的组
- o代表others， 所有其他用户
- a代表all，所有用户, 相当于 ugo
- "+" 是为指定的用户类型增加权限， "-"是去除指定用户类型的权限
- "=" 设置指定用户权限的设置，即将用户类型的所有权限重新设置
- r读，w写，x执行



## # 八进制数字模式

example：

```bash
chmod 777 file # 将目前目录下的所有文件与子目录皆设为任何人可读、写、执行
```



| #    | 权限           | rwx  | 二进制 |
| :--- | :------------- | :--- | :----- |
| 7    | 读 + 写 + 执行 | rwx  | 111    |
| 6    | 读 + 写        | rw-  | 110    |
| 5    | 读 + 执行      | r-x  | 101    |
| 4    | 只读           | r--  | 100    |
| 3    | 写 + 执行      | -wx  | 011    |
| 2    | 只写           | -w-  | 010    |
| 1    | 只执行         | --x  | 001    |
| 0    | 无             | ---  | 000    |

