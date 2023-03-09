## Regexp Syntax

> 正则表达式就是字符串集合的符号，当特定字符串位于正则表达式描述的集合中时，就表明该正则表达式匹配该串。

> 一些元字符`*+?()|`的匹配需要使用反斜杠+字符来进行匹配，例如 `\+` 匹配`+`

### 通配符

`.`表示通配符，可以匹配<span style="color:red">**任意**</span>字符

`\.`表示匹配`.`

### 匹配数字

`\d` 表示匹配 `0~9` 的任意<span style="color:red">**单个**</span>字符

### 匹配特定字符

用方括号`[]`括起来部分字符，表示匹配括号内的字符，匹配<span style="color:red">**单个**</span>字符

例如`can`,`man`,`fan`可以使用`[cmf]an`进行匹配

### 排除特定字符

`^`+`[]`可以用来实现<span style="color:red">排除</span>特定字符，注意这里的排除必须是方括号和`^`的结合，二者缺一不可，单独的`^`另有含义，表示开头

例如排除`hog`,`dog`,`bog`，可以使用`[^hdb]og`

### 字符范围

虽然`[]`可以表示一个字符集合，但如果我们想匹配一个连续范围内的字符，如果全部列出来则显得太过繁琐，

`-` 破折号（短横线）可以用来表示字符范围，例如`[0-6]` 表示只匹配<span style="color:red">0~6</span>的任意单个数字字符；同样的`[^n-p]` 表示只匹配字母<span style="color:red">n到p</span>以外的任意单个字符

同一组方括号中还可以使用多个字符范围，以及单个字符：

​	例如字母数字元字符`\w` 等价于 `[A-Za-z0-9_]`

### 匹配重复字符

可以使用花括号`{}`来包裹字符的重复次数，例如：

- `a{3}`表示匹配3个`a`；`a{1,3}`表示匹配字符`a`至少1次，至多3次
- `[wxy]{5}`表示匹配5个字符，每个字符可以是`w`,`x`或`y`
- `{}`中的最大重复次数可以省略，表示至多无限制；

### kleene操作符（*+）

- `\d*`表示<span style="color:red">0个或多个</span>数字字符
- `\d+`表示<span style="color:red">1个或多个</span>数字字符
- `a+`表示<span style="color:red">1个或多个</span>`a`
- `[abc]+` 表示<span style="color:red">1个或多个字符 (a、b 或 c)</span>

例如匹配`aaaabcc`,`aabbbbc`,`aacc`，跳过`a`可以使用`a+b*c+`来表示

### 可选字符

`?`表示可选性，表示匹配前面的<span style="color:red">**0个或1个字符或组**</span>

例如：`ab?c`可以匹配`abc`或`ac`因为`b`被认为是可选的

匹配：`1 file found?` `2 files found?` `24 files found?`; 跳过 `No files found.`

Ans：`\d+ files? found\?`

### 空白字符

`\s`能够匹配空白符: `␣`,`\t`,`\r`，同样可以使用`*`和`+`来进行重复

匹配`1. abc`  `2.  abc` `3.    abc` 跳过 `4.abc`

Ans：`\d\.\s+abc`

### 开始与结束

`^`和`$`表示一行的开始和结束

- `^success`表示只匹配以单词 `success` 开头的行
- `oper$`表示只匹配以 `oper` 结尾的行
- `^`和`$`同时使用表示匹配一个从开头到结尾一整行的模式

### 捕获组

正则表达式不仅可以用于**匹配**，还可以用于提取信息以便进一步处理，`()`圆括号内到字符会被捕获为一个组`group`

- `^(IMG\d+\.png)$`可以捕获和提取完整的图像文件名
- `^(IMG\d+)\.png$`可以捕获和提取图像的文件名（不含扩展名）

匹配：`file_record_transcript.pdf` `file_07241999.pdf` 捕获: `file_record_transcript` `file_07241999`

Ans：`^(file.+)\.pdf$`

捕获组可以嵌套：

- `^(IMG(\d+))\.png$`则可以捕获不含扩展名的图像文件名，以及当图片名含数字编号时，捕获其数字编号

### 条件

`|`搭配圆括号`()`可以来表示或关系的字符集

匹配：`I love cats` `I love dogs` ; Skip: `I love logs` `I love cogs`

Ans: `I\slove\s+(cats|dogs)`