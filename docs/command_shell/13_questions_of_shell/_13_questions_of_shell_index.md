# shell十三问
---------------------

### 系列目录
1. [为何叫做 shell?][1]	
 
2. [shell prompt(PS1) 与 Carriage Return(CR) 的关系？][2] (2008-10-30 02:05 最后更新)
	
3. [別人 echo、你也 echo ，是问 echo 知多少？][3]( 2008-10-30 02:08 最后更新)	

4. [" "(双引号) 与 ' '(单引号)差在哪？][4]  (2008-10-30 02:07 最后更新)
	
5. [var=value 在export前后差在哪？][5] (2008-10-30 02:12 最后更新)

6. [exec 跟 source 差在哪?][6] (2008-10-30 02:17 最后更新)
  
7. [( ) 与 { } 差在哪？][7]
	
8. [$(( )) 与 $( ) 还有${ } 差在哪？][8] (2008-10-30 02:20 最后更新)	

9. [$@ 与 $* 差在哪？][9]
	
10. [&& 与 || 差在哪？][10] (2008-10-30 02:21 最后更新)
	
11. [> 与 < 差在哪？][11] (2008-10-30 02:24 最后更新)
	
12. [你要 if 还是 case 呢?][12] (2008-10-30 02:25最后更新)
	
13. [for what? while 与 until 差在哪？][13] (2008-10-30 02:26最后更新)
	
14. [[^ ] 跟 [! ] 差在哪？][14]

15. [Part-I: Wildcard][14] (2008-10-30 02:25 最后更新)

16. [Part-II Regular Expression][15] (2008-10-30 02:26 最后更新)

17. [学习总结与原帖目录][16]
	
---------------------

[1]: command_shell/13_questions_of_shell/01_question_of_shell.md
[2]: command_shell/13_questions_of_shell/02_question_of_shell.md
[3]: command_shell/13_questions_of_shell/03_question_of_shell.md
[4]: command_shell/13_questions_of_shell/04_question_of_shell.md
[5]: command_shell/13_questions_of_shell/05_question_of_shell.md
[6]: command_shell/13_questions_of_shell/06_question_of_shell.md
[7]: command_shell/13_questions_of_shell/07_question_of_shell.md
[8]: command_shell/13_questions_of_shell/08_question_of_shell.md
[9]: command_shell/13_questions_of_shell/09_question_of_shell.md
[10]: command_shell/13_questions_of_shell/10_question_of_shell.md
[11]: command_shell/13_questions_of_shell/11_question_of_shell.md
[12]: command_shell/13_questions_of_shell/12_question_of_shell.md
[13]: command_shell/13_questions_of_shell/13_question_of_shell.md
[14]: command_shell/13_questions_of_shell/14_question_of_shell.md
[15]: command_shell/13_questions_of_shell/15_question_of_shell.md
[16]: command_shell/13_questions_of_shell/16_question_of_shell.md


### 参考资料
[Github: 13_questions_of_shell](https://github.com/wzb56/13_questions_of_shell)

### 小白点评
别问我为什么明明17问，却叫13问，it is old-fashioned.
<!-- 
说明：

1. 欢迎大家补充/扩充问题。

2. 我接触电脑的中文名称时是在台湾，因此一些术语或与大陆不同，请自行转换。 

3. 我会不定时"逐题"说明(以 Linux 上的 bash 为环境)
	同时，也会在任何时候进行无预警的修改。请读者自行留意。

4. 本人于本系列所发表的任文章均可自由以电子格式(非印刷)引用、修改、转载，
	且不必注明出处(若能注明 CU 更佳)。当然，若有错漏或不当结果，本人也不负任何责任。

5. 若有人愿意整理成册且付印者，本人仅保留著作权，版权收益之 30% 須捐赠于 CU 论坛管理者，剩余不究。

---------
建议參考谈论:

1. shaoping0330 兄关于变量替换的补充：（链接在改版后已经失效）

2. shaoping0330 兄[关于 RE][16] 的说明:

3. 关于 nested subshell 的讨论:（链接在改版后已经失效）

4. [关于 IFS 的讨论][18]: 
	
---------

* 感谢 lkydeer 兄整理 [word/pdf 版本][19]方便大家参考：
	
[shell-mardown]: https://github.com/wzb56/13_questions_of_shell
[0]: http://bbs.chinaunix.net/thread-218853-1-1.html
[1]: http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=218853&page=2#pid1454336
[2]: http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=218853&page=2#pid1467910
[3]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=3#pid1482452
[4]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=4#pid1511745
[5]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=5#pid1544391
[6]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=6#pid1583329
[7]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=6#pid1595135
[8]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=7#pid1617953
[9]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=7#pid1628522
[10]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=7#pid1634118
[11]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=7#pid1636825
[12]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=8#pid1679488
[13]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=8#pid1692457
[14]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=16#pid2930144
[15]: http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=16#pid2934852
[16]: http://bbs.chinaunix.net/forum/viewtopic.php?t=393964
[18]: http://bbs.chinaunix.net/forum/viewtopic.php?t=512925
[19]: http://bbs.chinaunix.net/viewthread.php?tid=963890&extra=page%3D2
 -->











 















































