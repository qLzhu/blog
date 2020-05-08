---
title: Graph:Easy绘制ASCII码（字符集）流程图
abbrlink: 44940
date: 2020-04-28 21:12:11
categories: mac
tags:
  - ASCII码
  - Graph Easy
  - http://asciiflow.com/
  - https://metacpan.org/pod/distribution/Graph-Easy/bin/graph-easy
---

## Install

HomeBrew的使用及其安装，请查阅 {% post_link homebrew-basic %}
cpan{%label info@(类似npm)%}是perl的软件包管理器，Mac系统集成了perl无需单独安装

```bash
brew install graphviz
cpan
# cpan shell -- CPAN exploration and modules installation (v2.00)
# Enter 'h' for help.
#
# cpan[1]>
#
# cpan 时一路 Next 安装就行
# 直到终端出现 cpan[1]> 字样，输入 exit 退出，再执行下述命令

sudo cpan Graph:Easy
# 问题：
# 初次 sudo cpan Graph:Easy 时未能成功
# 后来开启了 VPN 才安装了下来，大家可以试试，也有可能是我网络的问题

graph-easy --version
# Graph::Easy v0.76  (c) by Tels 2004-2008.  Released under the GPL 2.0 or later.
#
# Running under Perl v5.018004.
```
<!-- more -->

## rules

节点就是你要输出的某个单词，节点分为单节点和复合节点

1. 单节点 [xx]

```bash
echo "[ShangHai]" | graph-easy
graph-easy <<< "[ShangHai]"     #终端输出的另一种写法

# 输出：
+----------+
| ShangHai |
+----------+
```

2. 复合节点 [xx | xx | xx]

```bash
graph-easy <<< "[ShangHai | ShenZhen | BeiJing]"

# 输出：
+----------+----------+---------+
| ShangHai | ShenZhen | BeiJing |
+----------+----------+---------+

graph-easy <<< "[ShangHai | ShenZhen | BeiJing || China]"

# || 节点换行
# 输出：
+----------+----------+---------+
| ShangHai | ShenZhen | BeiJing |
+----------+----------+---------+
|  China   |
+----------+
```

3. 节点内的文本换行和对其

```bash
graph-easy <<< "[ShangHai ShenZhen\n\l Beijing\c LangFang\r QingHai]"

# \n 后面出现的文字开始换行
# \l 左对齐、\c 居中对齐、\r 右对齐，分别对应的单词是lefr\center\right
# 输出：
+-------------------+
| ShangHai ShenZhen |
|                   |
| Beijing           |
|     LangFang      |
|           QingHai |
+-------------------+
```

连接线，设置节点之间使用连接使用什么样式的线条

1. 无方向连接线，必须使用两个中划线 {%label info@(--)%}

```bash
graph-easy <<< "[ShangHai] -- [China]"

# 输出：
+----------+     +-------+
| ShangHai | --- | China |
+----------+     +-------+
```

2. 实线方向箭{%label info@(->)%}

```bash
graph-easy <<< "[ShangHai] -> [China]"

# 输出：
+----------+     +-------+
| ShangHai | --> | China |
+----------+     +-------+
```

3. 双向箭{%label info@(<->)%}

```bash
graph-easy <<< "[ShangHai] <-> [China]"

# 输出：
+----------+      +-------+
| ShangHai | <--> | China |
+----------+      +-------+
```

4. 双实线{%label info@(=>)%}
5. 点线{%label info@(.>)%}
6. 波浪线{%label info@(~>)%}
7. 虚线{%label info@(- >)%}
8. 点虚线{%label info@(.- >)%}
9. 点点虚线{%label info@(.- >)%}
10. \. \- \= \~可自由组合

回指

```bash
graph-easy <<< "[ShangHai] -> [China] -> [ShangHai]"

# 输出：
  +----------------+
  v                |
+----------+     +-------+
| ShangHai | --> | China |
+----------+     +-------+

graph-easy <<< "[ShangHai] -> [ShangHai]"

# 输出：
  +------+
  v      |
+----------+
| ShangHai |
+----------+
```

多指

```bash
graph-easy <<< "[ShangHai],[ShenZhen],[BeiJing] -> [China]"

# 输出：
+---------+     +----------+     +----------+
| BeiJing | --> |  China   | <-- | ShenZhen |
+---------+     +----------+     +----------+
                  ^
                  |
                  |
                +----------+
                | ShangHai |
                +----------+
```

css样式设置边框线{% label info@（其它css基础样式也行，样式必须放置在花括号内） %}

```bash
# easy_ascii.txt
[ShangHai] - message -> [China]{border:1px solid red}
[ShangHai] - message -> [China]{border:none}

# 线的粗细及其颜色，只有在输出成html时才会有效
# graph-easy easy_ascii.txt --output=index.html
```

群组线框{% label info@（()小括号扩起来） %}

```bash
graph-easy <<< "(group: [TongZhou] -> [BeiJing]) [BeiJing] -> [China]"

# 输出：
+ - - - - - - - - - - - - - - -+
' group:                       '
'                              '
' +----------+     +---------+ '     +-------+
' | TongZhou | --> | BeiJing | ' --> | China |
' +----------+     +---------+ '     +-------+
'                              '
+ - - - - - - - - - - - - - - -+
```

注释

1. 在生成ASCII码的源文件中注释使用#号加空格的方式
2. 连接线注解，节点连线之间的说明{%label info@(- 文字说明 ->)%}{%label danger@【注释两侧是有空格的，且注释文本是中文的话会乱】%}

```bash
graph-easy <<< "[ShangHai] - message -> [China]"
graph-easy <<< "[ShangHai] --> {label: "message"} [China]"

# 输出：
+----------+  message   +-------+
| ShangHai | ---------> | China |
+----------+            +-------+
```

连接方向

1. 通过{%label info@{flow:down}%}键值对设置
值有 west | left、east | right、north | up、south | down，可理解为地图的上北下南左西右东

```bash
graph-easy <<< "[ShangHai]{flow:down} - message -> [China]"

# 输出：
+----------+
| ShangHai |
+----------+
  |
  | message
  v
+----------+
|  China   |
+----------+
```

## output

除了上述示例中直接在终端输出外，Graph:Easy 还支持多格式的文件输出

1. \-\-input= 指定源文件

```bash
graph-easy --input=example.txt
graph-easy example.txt
```

2. \-\-output= 设置输出文件名及其格式

```bash
graph-easy example.txt --output=index.html
graph-easy example.txt index.html
```

3. \-\-xx 输出文件未设置格式时可使用此方法指定文件的格式

```bash
graph-easy example.txt index --html
```

更多信息请查阅 [Graph:Easy](http://bloodgate.com/perl/graph/manual/overview.html)
