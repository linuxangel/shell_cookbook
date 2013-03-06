######第一章Shell  之讨厌的正则 
 
*  思想（KISS）相当重要.
*	我们遵循KISS原则  （keep it simple，stupid） 
*   这事其实不难，只要按照以下思路进行就行了。 

>1.  把复杂的问题简单化，模块化 
>2.  写出每一个模块的伪码。 
>3.  组装你的模块 
>4.  写出shell code 
>5.   测试（test） 


####知道什么时候用什么实现，远比你知道怎么写code更重要。（shell不是万能的，择优取之。
####shell最精华，最美的地方在于有现成的命令可以使用，不用考虑过多的底层的问题。作为上
####层工作的最好的工具，所谓上层系统管理，网络管理等等。） 

###战略决定生死，细节决定成败 
##以下是我们以后将要更新的章节

*	讨厌的正则
*	可爱的变量
*	简单明了的输入输出
*	神奇的判断
*	不可不会的文件操作
*	瞬间飞升

###第一节讨厌的正则

*    之所以讨厌一个事物，是因为你驾驭不了它。

>1.	乱七八糟的符号。
>       举例：（grep  为行模式，默认以行显示）
>
>>  (1)
>>	`[root@localhost ~]# cat /etc/passwd | grep --color "r..t"`
>	`root:x:0:0:root:/root:/bin/bash`
>	`operator:x:11:0:operator:/root:/sbin/nologin`
>	`ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin`
>	`You have new mail in /var/spool/mail/root`
>	`[root@localhost ~]#`
>
显示三行

>>  (2)
>>	`[root@localhost ~]# cat /etc/passwd | grep --color 'root'`
>	`root:x:0:0:root:/root:/bin/bash`
>	`operator:x:11:0:operator:/root:/sbin/nologin`
>	`[root@localhost ~]# `
>	
>显示两行
>>	(3)
>>	`[root@localhost ~]# cat /etc/passwd | grep --color '^root'`
>`root:x:0:0:root:/root:/bin/bash`
>`[root@localhost ~]# `
> 
> 显示一行  
> 
>*	总结以上`:`什么是正则，两个单引号中间的部分就是正则。 
> 

*	正则有什么用？ 

> 1.	查找匹配 
>> 1)一个字符（如何在整篇文章中查找一个字符），正则提供了一些特殊符号，通过这
些符号去替换一些东西.
>>
>举例：在整篇文章中查找字母a 
>		
>>使用vi编辑器打开/etc/passwd 

>>在末行模式下查找a 
>输入   ：/a/  
>比如要查找abc，是一个或的关系,那么用 [ ] 。  
>>输入  ：/[abc]/      
>    ：/[a-c]/  
>	：/a|b|c/
>
>>中括号，把你想要定位的东西写入里面。
> 
>>`.`	任意一个字符。 
> 
>>`[]`  选择中括号里面的任意一个。 
> 
>>`[^]`  取非，取反 
> 
>>`[:alnum:]`阿尔法字符加数字 
> 
>>`[:alpha:]`任意一个字符`[[:alpha]]` 
> 
>>`[:digit:]`任意一个数字 `[[:digit:]]`（对它取反的话`[^[:digit:]]`）意为除了任意一个数字。 
> 
>>`[:lower:]`小写。  
> 
>>`[:upper:]`大写。
>
>>`[:space:]`空格。
>
>>`[:punct:]`标点。
>
>>*	总结`：`以上是如何定位一个字符。
>
> 
> 
> 
> 
> 
> 
> 
> 
