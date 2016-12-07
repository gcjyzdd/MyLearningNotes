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

### 4. 得到文件大小
`du -sh` is a good place to start. The options are (from man du):
```bash
   -s, --summarize
          display only a total for each argument

   -h, --human-readable
          print sizes in human readable format (e.g., 1K 234M 2G)
```

To check more than one directory and see the total, use du -sch:
```bash
   -c, --total
          produce a grand total
```

### 5. How to redirect output to a file and stdout
The command you want is named `tee`:
```bash
foo | tee output.file
```
For example:
```bash
ls -a | tee output.file
```
## 进程管理
1. 查询和关闭进程
find processes containing 'wine' and kill them
```
kill $(ps aux | grep 'wine' | awk '{print $2}')
```
bash 先执行$()内的命令, `awk '{print $2}'`得到第二列,即process id, pid,然后用kill关掉

## mount windows
在Ubuntu系统里加载windows系统
`sudo mount -o ro /dev/sda4 /media/changjie/Windows `

## merge and crop pdf

### merge multiple pdf files
We need the pdftk library to combine pdf files into one pdf. Install it in ubuntu
```bash
sudo apt install pdftk
```
The we can use pdftk to combine files:
```bash
pdftk f1.pdf f2.pdf f3.pdf output combine.pdf
```
If you have many files, we can use parameter expansion to achieve a clear command:
```bash
pdftk ./chapter{1..18}.pdf output book.pdf
```
In this way, we can merge many book chapters into one pdf book. It's really convienient. 

### crop pdf
Sometimes it's annoying to have so much empty space on a pdf page, especiaaly when we wanna print it. Therefore, we would like to remove unnecessary empty space on a pdf file. 

According to this question [How to crop a multi-page pdf file](http://askubuntu.com/questions/270493/how-to-crop-a-multi-page-image-scanned-pdf-file-which-wont-crop-with-pdfcrop), we can use the following bash script to crop pdf files.
```bash
#!/bin/bash

function usage () {
  echo "Usage: `basename $0` [Options] <input.pdf> [<output.pdf>]"
  echo
  echo " * Removes white margins from each page in the file. (Default operation)"
  echo " * Trims page edges by given amounts. (Alternative operation)"
  echo
  echo "If only <input.pdf> is given, it is overwritten with the cropped output."
  echo
  echo "Options:"
  echo
  echo " -m \"<left> [<top> [<right> <bottom>]]\""
  echo "    adds extra margins in default operation mode. Unit is bp. A single number"
  echo "    is used for all margins, two numbers \"<left> <top>\" are applied to the"
  echo "    right and bottom margins alike."
  echo
  echo " -t \"<left> [<top> [<right> <bottom>]]\""
  echo "    trims outer page edges by the given amounts. Unit is bp. A single number"
  echo "    is used for all trims, two numbers \"<left> <top>\" are applied to the"
  echo "    right and bottom trims alike."
  echo
  echo " -hires"
  echo "    %%HiResBoundingBox is used in default operation mode."
  echo
  echo " -help"
  echo "    prints this message."
}

c=0
mar=(0 0 0 0); tri=(0 0 0 0)
bbtype=BoundingBox

while getopts m:t:h: opt
do
  case $opt
  in
    m)
    eval mar=($OPTARG)
    [[ -z "${mar[1]}" ]] && mar[1]=${mar[0]}
    [[ -z "${mar[2]}" || -z "${mar[3]}" ]] && mar[2]=${mar[0]} && mar[3]=${mar[1]}
    c=0
    ;;
    t)
    eval tri=($OPTARG)
    [[ -z "${tri[1]}" ]] && tri[1]=${tri[0]}
    [[ -z "${tri[2]}" || -z "${tri[3]}" ]] && tri[2]=${tri[0]} && tri[3]=${tri[1]}
    c=1
    ;;
    h)
    if [[ "$OPTARG" == "ires" ]]
    then
      bbtype=HiResBoundingBox
    else
      usage 1>&2; exit 0
    fi
    ;;
    \?)
    usage 1>&2; exit 1
    ;;
  esac
done
shift $((OPTIND-1))

[[ -z "$1" ]] && echo "`basename $0`: missing filename" 1>&2 && usage 1>&2 && exit 1
input=$1;output=$1;shift;
[[ -n "$1" ]] && output=$1 && shift;

(
    [[ "$c" -eq 0 ]] && gs -dNOPAUSE -q -dBATCH -sDEVICE=bbox "$input" 2>&1 | grep "%%$bbtype"
    pdftk "$input" output - uncompress
) | perl -w -n -s -e '
  BEGIN {@m=split /\s+/, $mar; @t=split /\s+/, $tri;}
  if (/BoundingBox:\s+([\d\.\s]+\d)/) { push @bbox, $1; next;}
  elsif (/\/MediaBox\s+\[([\d\.\s]+\d)\]/) { @mb=split /\s+/, $1; next; }
  elsif (/pdftk_PageNum\s+(\d+)/) {
    $p=$1-1;
    if($c){
      $mb[0]+=$t[0];$mb[1]+=$t[1];$mb[2]-=$t[2];$mb[3]-=$t[3];
      print "/MediaBox [", join(" ", @mb), "]\n";
    } else {
      @bb=split /\s+/, $bbox[$p];
      $bb[0]+=$mb[0];$bb[1]+=$mb[1];$bb[2]+=$mb[0];$bb[3]+=$mb[1];
      $bb[0]-=$m[0];$bb[1]-=$m[1];$bb[2]+=$m[2];$bb[3]+=$m[3];
      print "/MediaBox [", join(" ", @bb), "]\n";
    }
  }
  print;
' -- -mar="${mar[*]}" -tri="${tri[*]}" -c=$c | pdftk - output "$output" compress
```
Usage:
```bash
#default operation
pdfcrop.sh orig.pdf cropped.pdf
pdfcrop.sh -m 10 orig.pdf cropped.pdf
pdfcrop.sh -hires orig.pdf cropped.pdf

#trimming pages
pdfcrop.sh -t "10 20 30 40" orig.pdf trimmed.pdf
```
## Turn off montor
To turn off the monitor `$ xset dpms force off`
To turn on the monitor `$ xset dpms force on`




~~~mermaid
graph LR;
    A[Hard edge] -->|Link text| B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
~~~