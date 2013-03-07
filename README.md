######第一章Shell  之讨厌的正则 
###linux 天使团团长：隋成龙
###编辑整理：邢耀东
* 思想（KISS）相当重要.
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

>*	乱七八糟的符号。
>       举例：（grep  为行模式，默认以行显示）
>
>>  (1)
>><pre><code>[root@localhost ~]# cat /etc/passwd | grep --color "r..t"
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
[root@localhost ~]# 
</code></pre>
>
>><p>显示三行</p>

>>  (2)
>><pre><code>[root@localhost ~]# cat /etc/passwd | grep --color 'root'
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
[root@localhost ~]#
</code></pre>
>><p>显示两行</p>
>>	(3)
>><pre><code>[root@localhost ~]# cat /etc/passwd | grep --color '^root'
root:x:0:0:root:/root:/bin/bash
[root@localhost ~]#
</code></pre>
>><p>显示一行 </p> 
> 
>>*	总结以上`:`什么是正则，两个单引号中间的部分就是正则。 
> 
>
>*    2.正则有什么用？</p> 
>
>***
>> <p>1.	查找匹配</p>
> 
>><p> 1)一个字符（如何在整篇文章中查找一个字符），正则提供了一些特殊符号，通过这
些符号去替换一些东西.</p>
>>
><p>举例：在整篇文章中查找字母a</p> 
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
>><p>简单的实例</p>
>><code><pre>[root@localhost ~]# cat testfile | grep --color '.'
dog pig Dog
pig pig .og
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# cat testfile | grep --color 'dog'
dog pig Dog
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# cat testfile | grep --color '[d|D]og'
dog pig Dog
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# cat testfile | grep --color '[^dD]og'
pig pig .og
[root@localhost ~]# cat testfile | grep --color -E 'd|p' 
dog pig Dog
pig pig .og
dog d.g dog dog dog
pig bird dog
[root@localhost ~]#
</code></pre>
>><p>备注  –E 代表的是一个扩展的表达方式。其中dp之间的  |  代表的是或的关系，任意取一。</p>

> 
>>转义字符  `\` 
>
>><p>举例：</p>
>
>>当 `.`  在这不转义的时候就代表是正则中的 .  代表的是匹配任意一个字符。 
> 
>><pre><code>[root@localhost ~]# cat testfile | grep --color -E 'd.g'
dog pig Dog
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# 
></code></pre>
>><p>当`\.`转义之后代表着文本中存在的具体的 `.`</p> 
> ***
>*	2.字符的边界，查找匹配多个字符用到字符边界。
>>
>>`^`开头（一行开头）	
>
>>`$`  结尾（一行结尾） 
> 
>>`\<`左边界
>
>>`\>`右边界     
>><pre><code>[root@localhost ~]# cat -A /etc/passwd
root:x:0:0:root:/root:/bin/bash$
bin:x:1:1:bin:/bin:/sbin/nologin$
daemon:x:2:2:daemon:/sbin:/sbin/nologin$
adm:x:3:4:adm:/var/adm:/sbin/nologin$
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin$
sync:x:5:0:sync:/sbin:/bin/sync$
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown$
halt:x:7:0:halt:/sbin:/sbin/halt$
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin$
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin$
operator:x:11:0:operator:/root:/sbin/nologin$
games:x:12:100:games:/usr/games:/sbin/nologin$
gopher:x:13:30:gopher:/var/gopher:/sbin/nologin$
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin$
.......
</code></pre>
>><p>以上结尾为`$`代表着这一行的字符边界。</p>
>><p>linux与windows之间格式在linux下面的转换方法</p>
>><p>命令`dos2unix` win格式转换linux </p>
>><p>命令`unix2dos`  linux格式转换成win</p>
>***
>*	3.重复
>><p> *  重复0次或多次。注意ls *  中的 * 不是正则，是shell中的通配符。正则中的*代表前面出现了0次或者多次。  * 〉=0 </p>
>><p>举例：匹配所有字符，字符每个出现次数大于等于0次 </p>
>><pre><code>
[root@localhost ~]# cat testfile |  grep --color '.*'
dog pig Dog
pig pig .og
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# </code></pre>
>>***
>><p>a的次数大于等于0次，字符中没有a，它就相当于没有，所以匹配所有。</p>
>><pre><code>[root@localhost ~]# cat testfile |  grep --color 'a*'
dog pig Dog
pig pig .og
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# </code></pre>
>>***
>><p>字符中有d，第一行出现一次。第二行没有出现等于0次，那么匹配。第三行一次，第四行没
有那就等于0次，那么匹配。</p>
>><pre><code>[root@localhost ~]# cat testfile |  grep --color 'd*'
dog pig Dog
pig pig .og
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# </code></pre>
>>***
>><p>匹配d，所有出现d的行都匹配出来。 </p>
>><pre><code>[root@localhost ~]# cat testfile |  grep --color 'd'
dog pig Dog
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# </code></pre>
>***
>><p>+匹配一个或者多个。+〉=1 </p> 
>><p>举例：匹配d，d+  表示每一行匹配d的次数大于等于1.</p>
>><pre><code>[root@localhost ~]# cat testfile |  grep --color -E 'd+'
dog pig Dog
dog d.g dog dog dog
pig bird dog
[root@localhost ~]#</code></pre>
>***
>><p>？匹配0个或者一个。〈=1 </p>
>><p>举例：匹配d，  d？  表示每一行匹配d的次数小于等于一次。</p>
>><pre><code>[root@localhost ~]# cat testfile |  grep --color -E 'd?'
dog pig Dog
pig pig .og
dog d.g dog dog dog
pig bird dog</code></pre>
>***
>><p>精确匹配次数。 </p>
>><p>{}  中填入数字代表匹配次数。大于等于次数的行被匹配出。</p> 
>><p>举例：a精确匹配5次。a出现的次数大于等于5次的行被匹配。</p>
>
>><pre><code>[root@localhost ~]# cat testfile 
dog pig Dog
pig pig .og
dog d.g dog dog dog
pig bird dog
aa
aaa
aaaa
aaaaa
aaaaa
aaaaaaa
aaaaaaaa
[root@localhost ~]# cat testfile |  grep --color 'a\{5\}'
aaaaa
aaaaa
aaaaaaa
aaaaaaaa
[root@localhost ~]# 
>></code></pre>
>***
>><p>精确到范围。</p> 
>><p>{n，m} </p>  
>><p>举例：匹配3至5，包括3和5.</p>
>><code><pre>[root@localhost ~]# cat testfile |  grep --color 'a\{3,5\}'
aaa
aaaa
aaaaa
aaaaa
aaaaaaa
aaaaaaaa
[root@localhost ~]# </code></pre>
>***
>*	4.组 
>*	组的重复
>><p>举例：pig一组，dog一组。其中的  | 有或的意思，如果有组pig或者dog，就匹配这一行。 </p>
>><pre><code>[root@localhost ~]# cat testfile |  grep --color -E '(pig|dog)+'
dog pig Dog
pig pig .og
dog d.g dog dog dog
pig bird dog
[root@localhost ~]# </code></pre>
>***
>><p>pig一组，dog一组。Pig或者dog出现的次数大于等于1次，就匹配这一行。 </p>
>><pre><code>[root@localhost ~]# cat testfile |  grep --color -E '(pig|dog)+'
dog pig Dog
pig pig .og
dog d.g dogdog !dog ddog!
pig bird dog
[root@localhost ~]# </code></pre>
>***
>><p>Pig一组，dog一组。Pig或者dog出现满足某一行精确到出现两次就匹配。</p>
>><pre><code>[root@localhost ~]# cat testfile |  grep --color -E '(pig|dog){2}'
dog d.g dogdog !dog ddog!
[root@localhost ~]#</code></pre>
>***
