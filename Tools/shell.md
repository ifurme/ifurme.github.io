### 设置主机名  
```bash  
hostnamectl set-hostname k8s-master  
```  
  
### 配置 sudo 免密  
```bash  
adduser lsspasswd lssusermod -aG wheel lssecho "lss ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
```  
  
### 配置 ssh 免密登陆  
```bash  
# 在本地主机生成密钥对， 并把公钥发送给远程主机  
ssh-keygen -t ecdsa -C "Key for deploy" -f DeployKey  
ssh-copy-id -i /home/lss/.ssh/DeployKey.pub lss@180.184.74.248  
```  
  
  
  
  
  
# 函数 function  
## 函数的定义  
```shell  
function fname() {  
    statement;}  
# 或者  
fname() {  
    statement;}  
# 或者  
fname() { statement; }  
```  
## 函数的参数  
```shell  
# 使用  
fname arg1 arg2 ;  
# 函数内部的引用  
function fname() {  
    echo $1, $2;  # 访问参数1和参数2  
    echo "$@";    # 以列表的方式一次性打印所有参数  
    echo "$*";    # 类似于上面，但是所有参数被视为单个实体  
    return 0;     # 返回值  
}  

# $0 表示脚本文件名
# $$ 当前Shell 进程ID
```  
  
# if   
- -d: dir  
```shell  
if [[ -d ${homeDir} ]]; then  # 如果homeDir是目录  
  statement;fi  
```  
- -f: file  
- -z: 若为空串, 则返回真 [[ -z $str ]]
- -n: 不为空串, 则返回真
  

# tr
`tr`是`translate`的缩写, 可以进行字符串的替换
```shell
# 调用格式
tr [option] set1 set2

➜  ~ echo "HELLO WORLD" | tr 'A-Z' 'a-z'
hello world
# 其中 A-Z 是连续的字符集合, 如果不连续, 则会被视为三个元素, 首个元素, - , 末尾元素;

# ROT13 是一个著名的加密算法, 在ROT13中,字符会被移动13个位置, 因此加密和解密可以使用同一个函数
➜  ~ echo "tr came, tr saw, tr conquered." | tr 'a-zA-Z' 'n-za-mN-ZA-M'
ge pnzr, ge fnj, ge pbadhrerq.
➜  ~ echo "ge pnzr, ge fnj, ge pbadhrerq." | tr 'a-zA-Z' 'n-za-mN-ZA-M'
tr came, tr saw, tr conquered.

# 使用 -d 指定要被删除的字符集合
cat file | tr -d set

# 使用 -c 指定字符集合的补集(非), 如果不给出set2, 则表示将不在set1中的字符删除
cat file | tr -c set1 set2

# 使用 -s 删除重复的字符, 只保留一个
➜  ~ echo "123    123  123 123   123" | tr -s " "
123 123 123 123 123
```
  
# regexp  
  
## 位置标记  
| regexp |        description         |         example         |  
|:------:|:--------------------------:|:-----------------------:|  
|  `^`   | 指定了匹配`regexp`的文本必须位于字符串的首部 | `^tux` 能够匹配以 `tux` 起始的行 |  
|  `$`   | 指定了匹配`regexp`的文本必须位于字符串的尾部 | `tux$` 能够匹配以 `tux` 结尾的行 |  
  
## 标识符  
|  regexp |             description             |                              example                              |  
|:--------:|:-----------------------------------:|:-----------------------------------------------------------------:|  
|  `A字符`   |           `regexp`必须匹配该字符           |                          `A`能够匹配字符`A`                            |  
|   `.`    |              匹配任意单个字符               |           `Hack.`能够匹配`Hackl`, `Hacki`等，但不能匹配`Hack12`等            |  
|   `[]`   | 匹配中括号内等任意**一个**字符, 中括号内可以是一个字符或字符范围 |           `cook[kl]`可以匹配`cool`或`cook`, [0-9]可以匹配任意单个数字            |  
|  `[^]`   |   匹配不在括号中等任意**一个**字符， 可以是单个字符或者范围   | `9[^01]`可以匹配`92`,`93`,但不能匹配`91`,`90`, `A[^0-9]`匹配A以及随后除数字外的任意字符                                                |  
  
## 数量修饰符  
|  regexp  |     description     |                      example                       |  
|:--------:|:-------------------:|:--------------------------------------------------:|  
|   `?`    | 匹配之前的项**1**次或**0**次 |  `colou?r` 可以匹配`color`, `colour`, 但不能匹配 `colouur`  |  
|   `+`    | 匹配之前的项**1**次或**多次** | `Rollo-9+` 可以匹配`Rollo-99`, `Rollo-9`, 但不能匹配`Rollo` |  
|   `*`    | 匹配之前的项**0**次或**多次** |          `co*l`可以匹配`cl`, `col`, `cooool`           |  
|  `{n}`   |    匹配之前的项**n**次     |               `[0-9]{3}` 能够匹配任意的三位数                |  
|  `{n,}`  |   之前的项至少匹配**n**次    |            `[0-9]{2,}` 能够匹配任意的两位或更多位的数字            |  
| `{n, m}` |  之前的项的匹配最小次数和最大次数   |              `[0-9]{2,5}` 能够匹配两位数到五位数              |  
  
## 特殊字符  
| regexp |   description    |                    example                    |  
|:------:|:----------------:|:---------------------------------------------:|  
|  `()`  |  将括号中的内容视为一个整体   |        `ma(tri)?x` 可以匹配`max`, `matrix`        |  
|  `｜`   | 逻辑或, 可以匹配两边的任意一项 |     `Oct(1st｜2nd)`可以匹配`Oct1st`, `Oct2nd`      |  
|  `\`   |    转义所有（特殊）字符    | `a\.b`可以匹配`a.b`， 但不能匹配`ajb`， 因为`\`忽略了`.`的特殊意义 |  
  
  
# grep  
  
## 在stdout中搜索pattern  
```shell  
ls | grep pattern
```  
## 在文件中搜索pattern  
```shell  
grep pattern filenamegrep "pattern" file1 file2 file3
```  
## 使用正则表达式  
```shell  
grep -E "[a-z]+" filenameegrep "[a-z]+" filename
```  
## `-o`只输出匹配到的文本  
```shell  
echo this is a line. | egrep -o "[a-z]\."line.  
```  
## `-v`反转匹配结果  
```shell  
# invert 输出不匹配的内容  
grep -v match_pattern filename  
```  
## `-c`统计匹配模式的文本行数  
```shell  
grep -c "text" filename
```  
## 统计匹配项的数量  
```shell  
echo -e "1234\nhello\n56" | egrep -o "[0-9]" | wc -l6  
```  
## `-n`显示匹配项的行号  
```shell  
grep pattern -n filename
```  
## `-l`显示匹配项所在的文件  
```shell  
grep -l pattern file1 file2# -L 则反转  
```  

# cut
`cut`命令可以按照列,而不是行来切分文件数据, 该命令可以用来处理固定宽度字段的文件、CSV文件或者按空格分隔的文件(例如标准日志文件)
## `-f`指定要提取的字段(列)
```shell
# field_list是要显示的列的集合,用逗号分隔
cut -f field_list filename
➜  nameless cat cutfile
1 cc 19
2 zs 20
3 aa 18
➜  nameless cut -d " " -f 1,2 cutfile
1 cc
2 zs
3 aa
# -d 指定分隔的字符, 默认是Tab, 
```
# sed
字符串替换, 用指定的字符串替换匹配模式, 模式可以是简单字符串或者正则表达式
```shell
# sed 后跟引号包括的串, 串以s打头, 后面跟3个分隔符包裹的两个串, 前者是匹配串, 后者是替换后的串, 分隔符可以自定义, s后面的字符会被视为分隔符, 如果串中包含分隔符, 可以使用反斜线转移
sed 's/pattern/replace_string/' file
sed 's:pattern:replace_string:' file
sed 's|pattern|replace_string|' file
# 默认只替换首次匹配的内容, 全局替换可以在引号包裹的末位添加g标识
sed 's:pattern:replace_string:g' file
# g标识前可以添加数字,表示从第几次出现的匹配串开始替换

# 正则表达式 ^$ 表示空行
# 使用 sed 移除空行 最后的 /d 表示不执行替换, 直接删除匹配到的行
sed '/^$/d' file

# 默认将替换后的内容输出到 stdout ,如果想要直接修改文件, 使用 -i 标识
# 最好先检查输出没有问题, 再往文件内写入, 或者使用 -i.bak 在进行替换的
# 同时, 创建原始文件的副本
sed 's/pattern/replacement/' -i file

# & 可以引用匹配到的串
echo this is an example | sed 's/\w\+/[&]/g' 
[this] [is] [an] [example]
# \w\+ 表示匹配每一个单词, 然后使用 [&] 替换它

# 子串匹配标记 \1 \2 ...
echo this is digit 7 in a number | sed 's/digit \([0-9]\)/\1' 
this is 7 in a number
# \(pattern\) 用以匹配子串(用小括号包裹), 然后替换为匹配到的串, 
# \1 表示替换为匹配到的第一个子串, 因为这里只有一个子串
```
# awk
逐行处理文件
```shell
# awk 可以接收3部分语句块, 每一部分都是可选的
awk 'BEGIN {statements} {statements} END {statements}'

# 输出文件行数
awk 'BEGIN {i=0} {i++} END {print i}' filename
# 如果有BEGIN语句块, 则会先执行, 之后每从文件中读取一行, 如果可以匹配中间语句块, 则执行, 最后如果有END语句块, 则执行

# awk 特殊变量
- NR: 记录编号, 当awk将行作为记录时, 就相当于行号
- NF: 字段数量, 默认以空格作为分隔符行内的字段数量
- $0: 当前记录的文本内容, 即该行的所有内容
- $1: 该行的第一个字段
- $2: 该行的第二个字段
- $...

➜  ~ echo -e "line1 f2 f3\nline2 f4 f5\nline3 f6 f6" | awk '{print "Line no: "NR", No of fields:"NF, "$0="$0, "$1="$1, "$2="$2, "$3="$3}'
Line no: 1, No of fields:3 $0=line1 f2 f3 $1=line1 $2=f2 $3=f3
Line no: 2, No of fields:3 $0=line2 f4 f5 $1=line2 $2=f4 $3=f5
Line no: 3, No of fields:3 $0=line3 f6 f6 $1=line3 $2=f6 $3=f6

# 统计文件行号
awk 'END {print NR}' filename
```

# case
```shell
#!/bin/bash

printf "Input integer number: "
read num

case $num in
    1)
        echo "Monday"
        ;;
    2)
        echo "Tuesday"
        ;;
    3)
        echo "Wednesday"
        ;;
    4)
        echo "Thursday"
        ;;
    5)
        echo "Friday"
        ;;
    6)
        echo "Saturday"
        ;;
    7)
        echo "Sunday"
        ;;
    *)
        echo "error"
esac
```
  
# set -euox pipefail  
  
## set -e  
出现异常立即退出  
```shell  
## Before  
#!/bin/bash  
  
# 'foo' is a non-existing command  
foo  
echo "bar"  
  
# output  
# ------  
# line 4: foo: command not found  
# bar  
  
## After  
#!/bin/bash  
set -e  
  
# 'foo' is a non-existing command  
foo  
echo "bar"  
  
# output  
# ------  
# line 5: foo: command not found  
```  
  
## set -o pipefail  
默认情况下，管道的最后一个操作成功，改行命令即成功，为了避免此情况  
```shell  
## Before  
#!/bin/bash  
set -e  
  
# 'foo' is a non-existing command  
foo | echo "a"  
echo "bar"  
  
# output  
# ------  
# a  
# line 5: foo: command not found  
# bar  
  
## After  
#!/bin/bash  
set -eo pipefail  
  
# 'foo' is a non-existing command  
foo | echo "a"  
echo "bar"  
  
# output  
# ------  
# a  
# line 5: foo: command not found  
```  
  
## set -u  
避免出现未定义的变量  
不建议使用, 可以用 ${VARNAME:-"default"} 进行默认赋值
```shell  
## Before  
#!/bin/bash  
set -eo pipefail  
  
echo $a  
echo "bar"  
  
# output  
# ------  
#  
# bar  
  
## After  
#!/bin/bash  
set -euo pipefail  
  
echo $a  
echo "bar"  
  
# output  
# ------  
# line 5: a: unbound variable  
```  
  
## set -x  
执行命令前打印命令  
```shell  
#!/bin/bash  
set -euxo pipefail  
  
a=5  
echo $a  
echo "bar"  
  
# output  
# ------  
# + a=5  
# + echo 5  
# 5  
# + echo bar  
# bar  
```  
  
# readonly  
  
```shell  
# 定义 变量、函数 为只读（即局部静态变量）  
readonly [-aAf] [name[=value] ...]  
-a: 指向数组  
-A: 指向关联数组（字典、map）  
-f: 指向函数  
-p: 显示全部只读变量  
# example  
readonly test='ok'  
test='my' # err: 试图修改只读变量  
```

# find
查找文件或目录
## `-print` 使用 `\n` 分隔输出的每个文件或者目录名
```shell
find . -print
```
`-print0` 使用空字符`\0`来分隔, 可以处理包含空白字符或换行符的文件名
```shell
> echo "test" > "file name"
> find . -type f -print | xargs ls -l
ls: cannot access ./file: No such file or directory
ls: cannot access name: No such file or directory
> find . -type f -print0 | xargs ls -l
> -rw-rw-rw-. 1 user group 5 ..... ./file name
```
```shell
 -type t
     True if the file is of the specified type.  Possible file types are as follows:

     b       block special
     c       character special
     d       directory
     f       regular file
     l       symbolic link
     p       FIFO
     s       socket
```
## `-name` 根据文件名搜索
```shell
# -name 指定文件名或通配符
# -iname 忽略大小写
find . -name '*.txt' -print
find . -iname 'example*' -print
```
## `-a` `-and` 与 `-o` `-or` 或
```shell
ls
new.txt some.jpg text.pdf stuff.png
find . \(-name '*.txt' -o -name '*.pdf' \) -print
./text.pdf
./new.txt
# \( \) 表示将括号中的内容视为一个整体
```
## `-regex` 根据正则表达式进行搜索

# date
输出日期
```shell
[mars@node1 shell]$ date
2022年 07月 21日 星期四 11:54:27 CST

# 以指定格式输出日期, 
# %d - 天
# %B - 月
# %Y - 年
# 格式化字符串用前缀 + 表示
➜  Ftc date "+%d %B %Y"
21 七月 2022

# %s - 自1970.1.1以来的秒数
# %S - 秒(0 ~ 60)
# %N - 纳秒数
# 生成当前时间戳
[mars@mars]$ date +%s%N
1658376079607893821

# 日期推移
date -d "30 days ago" +%Y-%m-%d
2022-07-21
date -d "30 days" +%Y-%m-%d
2022-08-20
```


https://niconiconi.fun/2018/10/21/unix-domain-socket/

# 公网IP
```shell
curl ifconfig.me
curl cip.cc
```

# 分隔字符串
## 1. 利用变量的字符串替换
```shell
# 将变量的值中的original替换为newstring
${var//original/newstring}
eg: file's content = a|b|c|d
filestr = $(head -n +1 file)
files = ${filestr//|/ }
for f in ${files[@]}
do ...
# 将｜替换为空格后，files就变成了一个数组
```


---


# 判断字符串取值

<table>
	<tr>
		<th> 表达式 </th> <th> 含义 </th>
	</tr>
	<tr>
		<th> ${var} </th> <th> 变量 var 的值， 与 $var 相同 </th>
	</tr>
	<tr>
		<td> ${var-DEFAULT} </td> <td rowspan="2"> 如果 var 没有被声明， 那么就以 $DEFAULT 作为其值 </td>
	</tr>
	<tr>
		<td> ${var=DEFAULT} </td>
	</tr>
	<tr>
		<td> ${var:-DEFAULT} </td> <td rowspan="2"> 如果 var 没有被声明， 或者其值为空，那么就以 $DEFAULT 作为其值 </td>
	</tr>
	<tr>
		<td> ${var:=DEFAULT} </td>
	</tr>
	<tr>
		<td> ${var+OTHER} </td> <td rowspan="2"> 如果 var 声明了， 那么其值就是 $OTHER, 否则就是 null 字符串 </td>
	</tr>
	<tr>
		<td> ${var:+OHTER} </td>
	</tr>
	<tr>
		<td> ${var?ERR_MSG} </td> <td rowspan="2"> 如果 var 没被声明，那么就打印 $ERR_MSG </td>
	</tr>
	<tr>
		<td> ${var:?ERR_MSG} </td>
	</tr>
	<tr>
		<td> ${!varprefix*} </td> <td rowspan="2"> 匹配之前所有以varprefix开头进行声明的变量 </td>
	</tr>
	<tr>
		<td> ${!varprefix@} </td>
	</tr>
</table>
  
---

# 字符串操作

| 表达式                           | 含义                                                                                 |
| -------------------------------- | ------------------------------------------------------------------------------------ |
| ${#string}                       | $string的长度                                                                        |
| ${string:position}               | 在 $string中，从位置 $position开始提取子串                                           |
| ${string:position:length}        | 在 $string中，从位置 $position开始提取长度为 $length的子串                           |
| ${string#substring}              | 从变量 $string的开头，删除最短匹配 $substring的子串                                  |
| ${string##substring}             | 从变量 $string的开头，删除最长匹配 $substring的子串                                  |
| ${string%substring}              | 从变量 $string的结尾，删除最短匹配 $substring的子串                                  |
| `${string%%substring}`           | 从变量 $string的结尾，删除最长匹配 $substring的子串                                  |
| ${string/substring/replacement}  | 使用 $replacement，来替代第一个匹配的 $substring                                     |
| ${string//substring/replacement} | 使用 $replacement，来替代所有匹配的 $substring                                       |
| ${string/#substring/replacement} | 如果 $string的前缀匹配 $substring, 那么就使用 $replacement 来代替匹配到的 $substring |

---

ssh 远程连接调用脚本（内含部分工具找不到）
https://stackoverflow.com/questions/53390425/kubectl-not-found-while-running-through-ssh-task-bamboo
