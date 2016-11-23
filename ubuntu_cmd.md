# Ubuntu 常用命令
[toc]

## 安装package
1. 从deb文件安装
`sudo dpkg -i file.deb`
有时会报错,说缺少dependent文件,这时需要运行
`dpkg -f`

## 创建Desktop lanucher
`Alt+F2`打开程序执行窗口,或者直接在terminal里面输入
```bash
gnome-desktop-item-edit --create-new ~/Desktop
```
然后在窗口里输入command和icon即可.

## 查找文件
###1. 在一定路径下查找匹配特定字符的文件
`find /path/to/dir -name 'name'`
记得一定要加引号,不然搜索中文时结果可能异常.例子
`find ~/Dwonloads` -name ' \*deb* '
`find`命令可以用来预览rm删除的效果,例如
```
find ./ -name '*([0-9])*'
rm ./ -name '(*[0-9]*)'
```

###2. delete a directory
`rm -rf /path/to/delete`
`-f` disable display
###3. grep 获得特定字符的文件列表
```bash
changjie@changjie-HP-ZBook-15:~/Downloads/开放电子书$ ls | grep 'linux'
kali-linux-doc.epub
linux-c-api-ref.epub
changjie@changjie-HP-ZBook-15:~/Downloads/开放电子书$ ls | grep 'Linux'
Java 程序员眼中的 Linux - v1.0.epub
Kali Linux Web 渗透测试秘籍.epub
Kali Linux 无线渗透测试入门指南.epub
Kali Linux 秘籍.epub
Linux 101 hacks 中文版.epub
Linux C 一站式学习.epub
Linux Inside 中文版.epub
Linux就该这么学 v1.02.pdf
Linux工具快速教程.epub
Linux 构建指南.epub
Linux设备驱动程序(中文第三版).pdf
The Linux Command Line 中文版.epub
大学霸 Kali Linux 安全渗透教程 fix1.epub
实验楼 Linux 教程合集 20160904 fix1.epub
嵌入式 Linux 知识库.epub
深入分析Linux内核源代码.pdf
理解Linux进程.epub
鸟哥的 Linux 私房菜：基础学习篇 第四版.epub
鸟哥的Linux私房菜：服务器架设篇 第三版.epub
changjie@changjie-HP-ZBook-15:~/Downloads/开放电子书$ ls | grep -i 'linux'
Java 程序员眼中的 Linux - v1.0.epub
kali-linux-doc.epub
Kali Linux Web 渗透测试秘籍.epub
Kali Linux 无线渗透测试入门指南.epub
Kali Linux 秘籍.epub
Linux 101 hacks 中文版.epub
linux-c-api-ref.epub
Linux C 一站式学习.epub
Linux Inside 中文版.epub
Linux就该这么学 v1.02.pdf
Linux工具快速教程.epub
Linux 构建指南.epub
Linux设备驱动程序(中文第三版).pdf
The Linux Command Line 中文版.epub
大学霸 Kali Linux 安全渗透教程 fix1.epub
实验楼 Linux 教程合集 20160904 fix1.epub
嵌入式 Linux 知识库.epub
```
`grep`默认大小写敏感,让其不敏感只要加 `-i`选项即可.

## 进程管理
1. 查询和关闭进程
find processes containing 'wine' and kill them
```
kill $(ps aux | grep 'wine' | awk '{print $2}')
```
bash 先执行$()内的命令, `awk '{print $2}'`得到第二列,即process id, pid,然后用kill关掉



~~~mermaid
graph LR;
    A[Hard edge] -->|Link text| B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
~~~