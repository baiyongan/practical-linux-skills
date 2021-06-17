# Bash 编程陷阱 60 例

本系列汇总了 Bash 编程中的一些常见错误，每个例子在某些方面都有缺陷。

---------------------

## 1. for f in \$(ls *.mp3)
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
 [阅读译文][1]


## 2. cp \$file \$target
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][2]


## 3. Filenames with leading dashes
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][3]


## 4. [ \$foo = "bar" ]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][4]
 

## 5. cd \$(dirname "\$f")
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][5]
 

## 6. [ "\$foo" = bar && "\$bar" = foo ]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][6]
 

## 7. [[ \$foo > 7 ]]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][7]
 

## 8. grep foo bar | while read -r; do ((count++)); done
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][8]
 
 
## 9. if [grep foo myfile]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][9]
 

## 10. if [bar="\$foo"]; then ...
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][10]


## 11. if [ [ a = b ] && [ c = d ] ]; then ...
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][11]

 
## 12. read \$foo
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][12]

 
## 13. cat file | sed s/foo/bar/ > file
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][13]

 
## 14. echo \$foo
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][14]


## 15. \$foo=bar
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][15]

 
## 16. foo = bar
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][16]

 
## 17. echo <<EOF
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][17]

 
## 18. su -c 'some command'
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][18]

 
## 19. cd /foo; bar
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][19]

 
## 20. [ bar == "\$foo" ]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][20]

 
## 21. for i in {1..10}; do ./something &; done
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][21]

 
## 22. cmd1 && cmd2 || cmd3
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][22]

 
## 23. echo "Hello World!"
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][23]

 
## 24. for arg in \$*
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][24]

 
## 25. function foo()
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][25]

 
## 26. echo "~"
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][26]

 
## 27. local var=\$(cmd)
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][27]
 
## 28. export foo=~/bar
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][28]

 
## 29. sed 's/\$foo/good bye/'
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][29]

 
## 30. tr [A-Z] [a-z]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][30]

 
## 31. ps ax | grep gedit
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][31]

 
## 32. printf "\$foo"
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][32]
 

## 33. for i in {1..\$n}
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][33]
 

## 34. if [[ \$foo = \$bar ]] (depending on intent)
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][34]
 

## 35. if [[ \$foo =~ 'some RE' ]]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][35]
 

## 36. [ -n \$foo ] or [ -z \$foo ]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][36]
 

## 37. [[ -e "\$broken_symlink" ]] returns 1 even though \$broken_symlink exists
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][37]
 

## 38. ed file <<<"g/d\{0,3\}/s//e/g" fails
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][38]
 

## 39. expr sub-string fails for "match"
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][39]
 

## 40. On UTF-8 and Byte-Order Marks (BOM)
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][40]
 

## 41. content=\$(<file)
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][41]
 

## 42. for file in ./* ; do if [[ \$file != *.* ]]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][42]
 

## 43. somecmd 2>&1 >>logfile
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][43]
 

## 44. cmd; (( ! \$? )) || die
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][44]
 

## 45. y=\$(( array[\$x] ))
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][45]
 

## 46. read num; echo \$((num+1))
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][46]
 

## 47. IFS=, read -ra fields <<< "\$csv_line"
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][47]
 

## 48. export CDPATH=.:~/myProject
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][48]
 

## 49. OIFS="\$IFS"; ...; IFS="\$OIFS"
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][49]
 

## 50. hosts=( \$(aws ...) )
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][50]
 

## 51. Non-atomic writes with xargs -P
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][51]
 

## 52. find . -exec sh -c 'echo {}' \;
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][52]
 

## 53. sudo mycmd > /myfile
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][53]
 

## 54. sudo ls /foo/*
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][54]
 

## 55. myprogram 2>&-
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][55]
 

## 56. Using xargs without -0
> [!NOTE]
> **涉及知识点**::
 </br></br><p align="right">
[阅读译文][56]
 

## 57. unset a[0]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][57]
 

## 58. month=\$(date +%m); day=\$(date +%d)
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][58]
 

## 59. i=\$(( 10#\$i ))
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][59]
 

## 60. set -euo pipefail
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][60]
 

### 60.1. errexit
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][60-1]
 

### 60.2. pipefail
> [!NOTE]
> **涉及知识点**::
 </br></br><p align="right">
[阅读译文][60-2]
 

### 60.3. nounset
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][60-3]
 

## 61. [[ -v hash[\$key] ]]
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][61]


## 62. (( hash[\$key]++ ))
> [!NOTE]
> **涉及知识点**:
 </br></br><p align="right">
[阅读译文][62]

## 参考资料
[★★★★★ | Bash Pitfalls](http://mywiki.wooledge.org/BashPitfalls)


## 小白点评
译文参考自上述参考资料中的 wiki 网站， 
截止到 2021-01-20 的内容，共计 60 个例子。
截止到 2021-06-16，共计 62 个例子。

---------------------

[1]: command_shell/bash_pitfalls/01_bash_pitfalls_case.md
[2]: command_shell/bash_pitfalls/02_bash_pitfalls_case.md
[3]: command_shell/bash_pitfalls/03_bash_pitfalls_case.md
[4]: command_shell/bash_pitfalls/04_bash_pitfalls_case.md
[5]: command_shell/bash_pitfalls/05_bash_pitfalls_case.md
[6]: command_shell/bash_pitfalls/06_bash_pitfalls_case.md
[7]: command_shell/bash_pitfalls/07_bash_pitfalls_case.md
[8]: command_shell/bash_pitfalls/08_bash_pitfalls_case.md
[9]: command_shell/bash_pitfalls/09_bash_pitfalls_case.md
[10]: command_shell/bash_pitfalls/10_bash_pitfalls_case.md
[11]: command_shell/bash_pitfalls/11_bash_pitfalls_case.md
[12]: command_shell/bash_pitfalls/12_bash_pitfalls_case.md
[13]: command_shell/bash_pitfalls/13_bash_pitfalls_case.md
[14]: command_shell/bash_pitfalls/14_bash_pitfalls_case.md
[15]: command_shell/bash_pitfalls/15_bash_pitfalls_case.md
[16]: command_shell/bash_pitfalls/16_bash_pitfalls_case.md
[17]: command_shell/bash_pitfalls/17_bash_pitfalls_case.md
[18]: command_shell/bash_pitfalls/18_bash_pitfalls_case.md
[19]: command_shell/bash_pitfalls/19_bash_pitfalls_case.md
[20]: command_shell/bash_pitfalls/20_bash_pitfalls_case.md
[21]: command_shell/bash_pitfalls/21_bash_pitfalls_case.md
[22]: command_shell/bash_pitfalls/22_bash_pitfalls_case.md
[23]: command_shell/bash_pitfalls/23_bash_pitfalls_case.md
[24]: command_shell/bash_pitfalls/24_bash_pitfalls_case.md
[25]: command_shell/bash_pitfalls/25_bash_pitfalls_case.md
[26]: command_shell/bash_pitfalls/26_bash_pitfalls_case.md
[27]: command_shell/bash_pitfalls/27_bash_pitfalls_case.md
[28]: command_shell/bash_pitfalls/28_bash_pitfalls_case.md
[29]: command_shell/bash_pitfalls/29_bash_pitfalls_case.md
[30]: command_shell/bash_pitfalls/30_bash_pitfalls_case.md
[31]: command_shell/bash_pitfalls/31_bash_pitfalls_case.md
[32]: command_shell/bash_pitfalls/32_bash_pitfalls_case.md
[33]: command_shell/bash_pitfalls/33_bash_pitfalls_case.md
[34]: command_shell/bash_pitfalls/34_bash_pitfalls_case.md
[35]: command_shell/bash_pitfalls/35_bash_pitfalls_case.md
[36]: command_shell/bash_pitfalls/36_bash_pitfalls_case.md
[37]: command_shell/bash_pitfalls/37_bash_pitfalls_case.md
[38]: command_shell/bash_pitfalls/38_bash_pitfalls_case.md
[39]: command_shell/bash_pitfalls/39_bash_pitfalls_case.md
[40]: command_shell/bash_pitfalls/40_bash_pitfalls_case.md
[41]: command_shell/bash_pitfalls/41_bash_pitfalls_case.md
[42]: command_shell/bash_pitfalls/42_bash_pitfalls_case.md
[43]: command_shell/bash_pitfalls/43_bash_pitfalls_case.md
[44]: command_shell/bash_pitfalls/44_bash_pitfalls_case.md
[45]: command_shell/bash_pitfalls/45_bash_pitfalls_case.md
[46]: command_shell/bash_pitfalls/46_bash_pitfalls_case.md
[47]: command_shell/bash_pitfalls/47_bash_pitfalls_case.md
[48]: command_shell/bash_pitfalls/48_bash_pitfalls_case.md
[49]: command_shell/bash_pitfalls/49_bash_pitfalls_case.md
[50]: command_shell/bash_pitfalls/50_bash_pitfalls_case.md
[51]: command_shell/bash_pitfalls/51_bash_pitfalls_case.md
[52]: command_shell/bash_pitfalls/52_bash_pitfalls_case.md
[53]: command_shell/bash_pitfalls/53_bash_pitfalls_case.md
[54]: command_shell/bash_pitfalls/54_bash_pitfalls_case.md
[55]: command_shell/bash_pitfalls/55_bash_pitfalls_case.md
[56]: command_shell/bash_pitfalls/56_bash_pitfalls_case.md
[57]: command_shell/bash_pitfalls/57_bash_pitfalls_case.md
[58]: command_shell/bash_pitfalls/58_bash_pitfalls_case.md
[59]: command_shell/bash_pitfalls/59_bash_pitfalls_case.md
[60]: command_shell/bash_pitfalls/60_bash_pitfalls_case.md
[60-1]: command_shell/bash_pitfalls/60-1_bash_pitfalls_case.md
[60-2]: command_shell/bash_pitfalls/60-2_bash_pitfalls_case.md
[60-3]: command_shell/bash_pitfalls/60-3_bash_pitfalls_case.md
[61]: command_shell/bash_pitfalls/61_bash_pitfalls_case.md
[62]: command_shell/bash_pitfalls/62_bash_pitfalls_case.md

<!-- 
编号|示例|涉及知识点
--|:--:|:--:
1 | for f in $(ls *.mp3)        | for 循环相关注意事项
2 | cp $file $target            |
3 | Filenames with leading dashes       |
4 | [ $foo = "bar" ]                |
5 | cd $(dirname "$f")              |
6 | [ "$foo" = bar && "$bar" = foo ]    |
7 | [[ $foo > 7 ]]                  |
8 | grep foo bar \| while read -r; do ((count++)); done  |
9 | if [grep foo myfile]            |
10 | if [bar="$foo"]; then ...           |
11 | if [ [ a = b ] && [ c = d ] ]; then ...     |
12 | read $foo               |
13 | cat file \| sed s/foo/bar/ > file            |
14 | echo $foo           |
15 | $foo=bar            |
16 | foo = bar           |
17 | echo <<EOF          |
18 | su -c 'some command'        |
19 | cd /foo; bar            |
20 | [ bar == "$foo" ]           |
21 | for i in {1..10}; do ./something &; done        |
22 | cmd1 && cmd2 || cmd3            |
23 | echo "Hello World!"         |
24 | for arg in $*           |
25 | function foo()          |
26 | echo "~"                |
27 | local var=$(cmd)            |
28 | export foo=~/bar            |
29 | sed 's/$foo/good bye/'      |
30 | tr [A-Z] [a-z]              |
31 | ps ax \| grep gedit          |
32 | printf "$foo"               |
33 | for i in {1..$n}            |
34 | if [[ $foo = $bar ]] (depending on intent)      |
35 | if [[ $foo =~ 'some RE' ]]              |
36 | [ -n $foo ] or [ -z $foo ]              |
37 | [[ -e "$broken_symlink" ]] returns 1 even though $broken_symlink exists     |
38 | ed file <<<"g/d\{0,3\}/s//e/g" fails            |
39 | expr sub-string fails for "match"           |
40 | On UTF-8 and Byte-Order Marks (BOM)         |
41 | content=$(<file)                |
42 | for file in ./* ; do if [[ $file != *.* ]]      |
43 | somecmd 2>&1 >>logfile              |
44 | cmd; (( ! $? )) || die              |
45 | y=$(( array[$x] ))                  |
46 | read num; echo $((num+1))           |
47 | IFS=, read -ra fields <<< "$csv_line"           |
48 | export CDPATH=.:~/myProject     |
49 | OIFS="$IFS"; ...; IFS="$OIFS"       |
50 | hosts=( $(aws ...) )            |
51 | Non-atomic writes with xargs -P     |
52 | find . -exec sh -c 'echo {}' \;     |
53 | sudo mycmd > /myfile            |
54 | sudo ls /foo/*              |
55 | myprogram 2>&-              |
56 | Using xargs without -0      |
57 | unset a[0]          |
58 | month=$(date +%m); day=$(date +%d)      |
59 | i=$(( 10#$i ))          |
60 | set -euo pipefail       | 
-->
