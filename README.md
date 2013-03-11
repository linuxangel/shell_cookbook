######第一章Shell  之讨厌的正则 
###linux 天使团团长：隋成龙
###编辑整理：邢耀东
###代码测试：韩国梁
###版本美化: 杨飞
* 思想（KISS）相当重要.
*	我们遵循KISS原则  （keep it simple，stupid） 
*   这事其实不难，只要按照以下思路进行就行了。 

>1.  把复杂的问题简单化，模块化 
>2.  写出每一个模块的伪码。 
>3.  组装你的模块 
>4.  写出shell code 
>5.   测试（test） 


####<p>知道什么时候用什么实现，远比你知道怎么写code更重要。（shell不是万能的，择优取之。
shell最精华，最美的地方在于有现成的命令可以使用，不用考虑过多的底层的问题。作为上
层工作的最好的工具，所谓上层系统管理，网络管理等等。）</p> 

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


###第二章Shell之可爱的变量###


<p>知止，知道正则在什么时候停止。</p>
<p><I>知止而后能定，定而后能静，静而后能安，安而后能虑，虑而后能得。
									<br>&copy;语出</I>《大学》</p>
<p>正则本身是个贪婪模式，当你发现 `.*` 会匹配所有的东西。所以应该知道正则在哪停止。</p>
<p>我们的课程是一门有思想的shell课程.</p>
<p>######知识列表######</p>
<p>1.	bash的通配符</p>
<p>2.	设定变量</p>
<p>3.	变量的工作范围</p>
<p>4.	变量的一些有意思的用法</p>
<p>5.	有用的自带变量</p>
<p>6.	有趣的操作符号</p>
<p>7.	数学运算与数组</p>

* 一.	bash通配符

<p>shell常见通配符</p>
<table>
<tr>
<th><I>字符</I></th>
<th><I>含义</I></th>
<th><I>实例</I></th>
</tr>
<tr>
<th><I>*</I></th>
<th><I>匹配0或多个字符</I></th>
<th><I>a*b a与b之间可以有任意长度的任意字符，也可以一个也没有，如：aabcb，axyzb，a012b，ab.</I></th>
</tr>
<tr>
<th><I>？</I></th>
<th><I>匹配任意字符</I></th>
<th><I>a？b a与b之间必须也只能由一个字符，可以是任意字符，如aab，abb，acb，a0b.</I></th>
</tr>
<tr>
<th><I>[list]</I></th>
<th><I>匹配list中的任意单一字符</I></th>

<th><I>a[xyz]b a与b之间必须也只能有一个字符，但只能是x或y或z，如axb，ayb，azb. </I></th>
</tr>
<tr>
<th><I>[！list]</I></th>
<th><I>匹配除list中的任意单一字符</I></th>
<th><I>a[！0-9]b a与b之间必须也只能有一个字符，但不能是除阿拉伯数字.</I></th>
</th>
<tr>
<th><I>[c1-c2]</I></th>
<th><I>匹配c1-c2中的任意单一字符如：[0-9]</I></th>
<th><I>a[0-9]b 0-9之间必须也只能有一个字符，如a0b，a1b，a2b…a9b.</I></th>
</tr>
<tr>
<th><I>{string1,string2,…}</I></th>
<th><I>匹配string1或者string2（或者更多）其一字符串</I></th>
<th><I>a{abc，xyz，123}b a与b之间只能是abc或xyz或123 这三个字符串之一.</I></th>
</tr>
</table>

<p>注：此处的 * 和正则中 * 的区别，正则中 * 始终出现在命令的参数部分。</p>
<p>比如 ls * 中的* 代表的是通配符，而`grep ‘.*’/etc/passwd` 代表正则。</p>
思考：`mkdir –pv /tmp/{a，b}/{1，2，3}`一共创建了多少个文件？
[：digit：] 在这里任然适用
查看一个文件名为数字文件ls –lh `[[：digit：]]`

* 二． 设定变量。


	1〉变量名=值
格式的要求“开头必须是字母或者下划线”，“=两边不能有空格”

	2〉取消变量

	unset 变量名

举例：
<pre><code>[root@localhost ~]# b=123 
[root@localhost ~]# a=hello
[root@localhost ~]# echo $a
hello
[root@localhost ~]# echo $b
123
[root@localhost ~]# unset a
[root@localhost ~]# echo $a

[root@localhost ~]#
</code></pre>
<p>在其他编程语言中需要区分变量类型，在shell中是不区分的，拿以上变量来说都是一个变量。</p>

*	三.	变量的工作范围
<p>设定一个变量在什么样一个范围内会生效，在什么时候继承下去。</p>
举例：
<pre><code>
[root@localhost ~]# a=123
[root@localhost ~]# bash
[root@localhost ~]# echo $a

[root@localhost ~]# 
</code></pre> 

<p>bash命令是在当前的进程产生一个子进程。</p>
<p>父bash `a=123`</p>
<p>子进程bash 在子进程中父进程的变量不生效。所以以上例子中没</p>
<p>通过pstree命令可以看出整个过程。最顶端的那个进程为init，init为所有进程的父进程</p>

<pre><code>
[root@localhost ~]# pstree
init─┬─acpid
     ├─atd
     ├─auditd─┬─audispd───{audispd}
     │        └─{auditd}
     ├─automount───4*[{automount}]
     ├─avahi-daemon───avahi-daemon
     ├─crond
     ├─cupsd
     ├─dbus-daemon───{dbus-daemon}
     ├─2*[dhclient]
     ├─events/0
     ├─gam_server
     ├─gpm
     ├─hald───hald-runner─┬─hald-addon-acpi
     │                    ├─hald-addon-keyb
     │                    └─hald-addon-stor
     ├─hcid
     ├─hidd
     ├─httpd───10*[httpd]
     ├─khelper
     ├─klogd
     ├─krfcommd
     ├─ksoftirqd/0
     ├─kthread─┬─aio/0
     │         ├─ata/0
 .....
</code></pre>

<p>在当前shell中打开一个shell，使用bash命令。退出子shell，使用exit.</p>
<p>变量只在当前生效：</p>
<pre><code>
[root@localhost ~]# a=123
[root@localhost ~]# bash
[root@localhost ~]# echo $a

[root@localhost ~]# 
</code></pre> 
<p>变量继承，在所有子进程中生效。export 命令向下影响所有的子进程。</p>
举例：
<pre><code>
[root@localhost ~]# a=123
[root@localhost ~]# export a
[root@localhost ~]# bash
[root@localhost ~]# echo $a
123
[root@localhost ~]#
</code></pre>
*	系统变量和环境变量
<p>set 返回结果包含环境变量和局部变量。</p>
<p>env 返回结果包含环境变量。</p>
<pre><code>
[root@localhost ~]# set
BASH=/bin/bash
BASH_ARGC=()
BASH_ARGV=()
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="3" [1]="2" [2]="25" [3]="1" [4]="release" [5]="i686-redhat-linux-gnu")
BASH_VERSION='3.2.25(1)-release'
COLORS=/etc/DIR_COLORS
COLUMNS=168
CVS_RSH=ssh
DIRSTACK=()
EUID=0
GROUPS=()
G_BROKEN_FILENAMES=1
HISTFILE=/root/.bash_history
HISTFILESIZE=1000
HISTSIZE=1000
HOME=/root
HOSTNAME=localhost.localdomain
HOSTTYPE=i686
IFS=$' \t\n'
INPUTRC=/etc/inputrc
LANG=zh_CN.UTF-8
LESSOPEN='|/usr/bin/lesspipe.sh %s'
.....
</code></pre>

<pre><code>
[root@localhost ~]# env
HOSTNAME=localhost.localdomain
SHELL=/bin/bash
TERM=linux
HISTSIZE=1000
SSH_CLIENT=172.16.10.101 55656 22
SSH_TTY=/dev/pts/0
USER=root
LS_COLORS=no=00:fi=00:di=01;34:ln=01;36:pi=40;33:so=01;35:bd=40;33;01:cd=40;33;01:or=01;05;37;41:mi=01;05;37;41:ex=01;32:*.cmd=01;32:*.exe=01;32:*.com=01;32:*.btm=01;32:*.bat=01;32:*.sh=01;32:*.csh=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.gz=01;31:*.bz2=01;31:*.bz=01;31:*.tz=01;31:*.rpm=01;31:*.cpio=01;31:*.jpg=01;35:*.gif=01;35:*.bmp=01;35:*.xbm=01;35:*.xpm=01;35:*.png=01;35:*.tif=01;35:
PATH=/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
MAIL=/var/spool/mail/root
PWD=/root
INPUTRC=/etc/inputrc
a=123
LANG=zh_CN.UTF-8
HOME=/root
SHLVL=4
LOGNAME=root
CVS_RSH=ssh
SSH_CONNECTION=172.16.10.101 55656 172.16.10.104 22
LESSOPEN=|/usr/bin/lesspipe.sh %s
G_BROKEN_FILENAMES=1
_=/bin/env
[root@localhost ~]#
</code></pre>
<p>所谓系统变量：自己定义的一个变量就是系统变量。</p>
<p>环境变量:通过env返回的结果为环境变量。</p>
<p>以下四个变量的执行顺序是怎么排列：</p>
<pre><code>
/etc/ profile
~/.bash_profile
~/.bashrc
/etc/bashrc
</code></pre>
<p>分别定义同一个变量不同的返回值。</p>
<pre><code>/etc/ profile 中定义 a=123
~/.bash_profile 中定义 a=234
~/.bashrc 中定义 a=345
/etc/bashrc a=456
</code></pre>
<p>那么其执行的先后顺序如下:</p>
<pre><code>
/etc/profile
/root/.bash_profile
/root/.bashrc
/etc/bashrc
</code></pre>

<p>登陆shell和非登陆shell</p>
<p>登陆shell是指当用户登陆系统时所取得的那个shell，通过查找以上四个不同的启动文件来处理其中的命令，bash处理顺序如下：</p>
	/etc/profile-----/$home/.bash_profile------/$home/.bashrc-------/etc/bashrc
<p>非登陆shell</p>
<p>`/etc/bashrc` 尽管不是通过bash直接调用，但许多`~/.bashrc`文件调用`/etc/bashrc`。这种设置使得超级用户可以为系统内的非登陆shell建立默认属性。</p>
<p>`.bashrc`非登陆shell执行`~/.bashrc`文件中的命令，而登陆shell的启动文件（如`.bash_profile`）通常会运行这个文件。这样，登陆shell和非登陆shell都可以运行`.bashrc`中的命令了。</p>

<p>典型的例子 su 和su –</p>
<p>su – 登陆shell</p>
<p>su 非登陆shell</p>
<p>总结一下shell内置的环境变量有哪些？</p>
***
*	四． 变量的有意思的用法。

<pre><code>
[root@localhost ~]# a=
[root@localhost ~]# echo ${a:-hello}
hello
[root@localhost ~]# echo $a

[root@localhost ~]#
</code></pre>
<pre><code>
[root@localhost ~]# a=123
[root@localhost ~]# echo ${a:-hello}
123
[root@localhost ~]# echo $a
123
[root@localhost ~]# 
</code></pre>
<p>对比，当a没有值得时候，临时使用hello。当a有值的时候输出a的值。</p>
<p>总结：`${var:-word} `如果var没有值，则临时设定值为`word`</p>
***
<pre><code>
[root@localhost ~]# a=
[root@localhost ~]# echo ${a:=hello}
hello
[root@localhost ~]# echo $a
hello
[root@localhost ~]# 

</code></pre>
<pre><code>
[root@localhost ~]# a=123
[root@localhost ~]# echo ${a:=hello}
123
[root@localhost ~]# echo $a
123
[root@localhost ~]# 

</code></pre>
<p>对比以上，当a没有值的时候，会设定为hello。当a有值时输出a的值。</p>
<p>总结 ：${var:=word}如果var没有值，设定为word。当var有值输出a的值。</p>
***
<pre><code>
[root@localhost ~]# a=
[root@localhost ~]# echo ${a:+hello}

[root@localhost ~]# echo $a

[root@localhost ~]# 

</code></pre>
<pre><code>
[root@localhost ~]# a=123
[root@localhost ~]# echo ${a:+hello}
hello
[root@localhost ~]# echo $a
123
[root@localhost ~]# 
</code></pre>
<p>对比以上，当a有值时，会临时使用hello，但是值本身不改变。</p>
<p>总结：${var:+word} 如果var有值，临时设定为word，但是var本身的值不改变。</p>
***
<pre><code>
[root@localhost ~]# a=
[root@localhost ~]# echo ${a:?hello}
bash: a: hello
[root@localhost ~]# 

</code></pre>
<pre><code>
[root@localhost ~]# a=123
[root@localhost ~]# echo ${a:?hello}
123
[root@localhost ~]# echo $a
123
[root@localhost ~]#

</code></pre>
<p>对比以上,如果a没有值，报错，如果a有值打印出a的值；</p>
<p>总结：${var:?word} 如果var没有值，报错，如果var有值打印出var的值。</p>
***
<pre><code>
[root@localhost ~]# a=hello
[root@localhost ~]# echo ${a:0}
hello
[root@localhost ~]# echo ${a:1}
ello
[root@localhost ~]# echo ${a:2}
llo
[root@localhost ~]# echo ${a:3}
lo
[root@localhost ~]# echo ${a:4}
o
[root@localhost ~]# 
</code></pre>
<pre><code>
[root@localhost ~]# a=hello
[root@localhost ~]# echo ${a:1:3}
ell
[root@localhost ~]#

</code></pre>
<p>对比以上</p>
<p>总结：${var:offset}从变量offset位置开始，输出到结束</p>
<p>$(var:offset：length)从变量offset开始，输出length</p>
***
<pre><code>
[root@localhost ~]# a=/usr/bin/bash
[root@localhost ~]# echo ${a%/*}
/usr/bin
[root@localhost ~]# 

</code></pre>
<pre><code>
[root@localhost ~]# a=/usr/bin/bash
[root@localhost ~]# echo ${a%%/*}  

[root@localhost ~]#

</code></pre>
<p>从a的值的结尾开始往前数，进行最小匹配，删除匹配部分。返回的是`/usr/bin`</p>
<p>从a的值的结尾开始往前数，进行最大的匹配，并删除匹配的部分，那么返回值为空</p>
<p>总结：${var%模式}从尾部开始，进行最小匹配,,然后删除匹配部分。</p>
<p>${var%%模式}从尾部开始，进行最大匹配，然后删除匹配部分。</p>
***
<pre><code>
[root@localhost ~]# a=/usr/bin/bash
[root@localhost ~]# echo ${a#/*}   
usr/bin/bash
[root@localhost ~]# 

</code></pre>
<pre><code>
[root@localhost ~]# a=/usr/bin/bash
[root@localhost ~]# echo ${a##/*}  

[root@localhost ~]# 


</code></pre>
<p>以`/`分割，从左开始第一个/之前的部分（包括/）匹配，那么删除之前的部分。</p>
<p>以`/`为分割，从左开始第一个直到结尾最后一个/的内容最大匹配，那么删除之前的部分。</p>
<p>总结：`${var#模式}`从头部开始，进行最小匹配，然后删除匹配部分。</p>
<p>`${var##模式}`从头部开始，进行最大匹配，然后删除匹配部分。</p>
***
<pre><code>[root@localhost ~]# a=nalmelaminceaneak
[root@localhost ~]# echo ${#a}
17
[root@localhost ~]# 

</code></pre>
<pre><code>
[root@localhost ~]# a=nsnkdknekalsmle1111
[root@localhost ~]# echo ${#a}           
19
[root@localhost ~]# 

</code></pre>
<p>变量之前的#是可以用来计算变量值中的字符串的。</p>
<p>总结：${#var} 用来计算变量字符串。</p>
<p>以上部分作为在shell中字符串的截取。其中 * 通配符，还可以用 ？号</p>
***
<pre><code>
[root@localhost ~]# echo ${!P*}
PATH PIPESTATUS PPID PS1 PS2 PS4 PWD
[root@localhost ~]# 

</code></pre>
<p>打印出系统中以P开头的变量。</p>
<p>总结： `${!var@}`</p>
<p>`${!var*}`</p>
<p>打印系统中所有以var开头的变量。两者是一样的，唯一不同，@整个当字符串处理，*每个当字符串处理。（详细讲解，在以后循环章节再补充）</p>
***
*	五． 有用的自带变量
<p>$0,$1,$2…$9 $0代表脚本本身，$1参数的位置。注意当你的参数大于10时，变量的写法${10}
,在10上加上 { } 。</p>
<p>$#用来计算参数的个数。</p>
<p>$* 与$@ 显示该脚本所有参数。</p>
<p>举例：如下</p>

<pre><code>
[root@localhost ~]# vi test.sh
#!/bin/bash
echo \$1=$1
echo \$2=$2
echo \$3=$3
echo \$4=$4
echo \$5=$5
echo \$6=$6
echo \$7=$7
echo \$8=$8
echo \$9=$9
echo \$10=${10}

echo \$#=$#
echo \$*=$*
echo \$@=$@
[root@localhost ~]# sh test.sh a b c d e f j k l m n 
$1=a
$2=b
$3=c
$4=d
$5=e
$6=f
$7=j
$8=k
$9=l
$10=m
$#=11
$*=a b c d e f j k l m n
$@=a b c d e f j k l m n
[root@localhost ~]# 
</code></pre>
***
<p>$$ 指当前shell的PID</p>
举例：
<pre><code>[root@localhost ~]# echo $$
25015
[root@localhost ~]# 
</code></pre>

***
<p>$_ 上一条命令的最后一个参数。</p>
举例：

<pre><code>[root@localhost ~]# ps aux >/dev/null 
[root@localhost ~]# echo $_
aux
[root@localhost ~]# 
</code></pre>
<pre><code>
[root@localhost ~]# ps -eo pid,ppid,comm >/dev/null 
[root@localhost ~]# echo $_
pid,ppid,comm
[root@localhost ~]# grep -b -n root /etc/passwd
1:0:root:x:0:0:root:/root:/bin/bash
12:416:operator:x:11:0:operator:/root:/sbin/nologin
[root@localhost ~]# 
</code></pre>
<p>写这样一个脚本，只能在一个会话执行,能办到么？</p>
***
<p>$- 用来查看shell是否交互（himBH），其中有i就是交互式。</p>
举例：
<pre><code>[root@localhost ~]# echo $-
himBH
[root@localhost ~]# 
</code></pre>
***

<p>$! 显示最后一个进入后台的作业的pid</p>
<pre><code>
[root@localhost ~]# sleep 10 &
[1] 25652
[root@localhost ~]# echo $!
25652
[root@localhost ~]#
</code></pre>
***
<p>$? 上一条命令的返回值，0成功，非0部成功。</p>
<pre><code>
[root@localhost ~]# test
[root@localhost ~]# echo $?
1
[root@localhost ~]# cd /
[root@localhost /]# echo $?
0
[root@localhost /]# 
</code></pre>
***
*	六． 有趣的操作符号
<p>&& 前面的命令执行成功，则执行后面的命令执行</p>
<p>|| 前面的命令执行不成功，则执行后面的命令执行</p>
<p>`;` 命令的分割符号。</p>
举例：
<pre><code>
[root@localhost ~]# echo hello | grep a && echo "匹配"
[root@localhost ~]# echo hello | grep a || echo "不匹配"   
不匹配
[root@localhost ~]# echo hello | grep a && echo "匹配" || echo "不匹配"
不匹配
[root@localhost ~]# echo hello | grep hello && echo "匹配" || echo "不匹配" 
hello
匹配
[root@localhost ~]# echo hello | grep hello ; echo "匹配" || echo "不匹配"  
hello
匹配
[root@localhost ~]# echo hello | grep hello ; echo "匹配" ; echo "不匹配"  
hello
匹配
不匹配
[root@localhost ~]# echo hello | grep a ; echo "匹配" ; echo "不匹配"      
匹配
不匹配
[root@localhost ~]# 
</code></pre>

<p>&& || 这两个符号太长的时候，关键看它之前执行之后返回的结果。</p>
<p>； 前面命令不管成不成功都执行。</p>
<p>& 后台操作符号。其实它可以实现并发的效果。</p>
<p>例如：两个脚本</p>
<pre><code>[root@localhost ~]# vi ping.sh
#!/bin/bash
ping 172.16.10.1 -c 5 &
ping 172.16.10.101 -c 5 &
ping 172.16.10.104 -c 5 &
***
[root@localhost ~]# vi ping.sh1 
#!/bin/bash
ping 172.16.10.1 -c 5
ping 172.16.10.101 -c 5
ping 172.16.10.104 -c 5
</code></pre>
<p>执行两个脚本后所用的时间对比</p>
<pre><code>
[root@localhost ~]# time `sh ping.sh` 
real    0m4.987s
user    0m0.000s
sys     0m0.017s
[root@localhost ~]# time `sh ping.sh1`
real    0m15.018s
user    0m0.002s
sys     0m0.039s
[root@localhost ~]#
</code></pre>

<p>讨论并发的问题</p>
<p>（） 合并输出，（）里面的命令在子shell中执行。</p>
举例：
<pre><code>
[root@localhost ~]# cat /tmp/a
a
[root@localhost ~]# cat /tmp/b
b
[root@localhost ~]# cat /tmp/a ; cat /tmp/b > /tmp/ab 
a
[root@localhost ~]# cat /tmp/ab 
b
[root@localhost ~]# (cat /tmp/a ; cat /tmp/b) > /tmp/ab 
[root@localhost ~]# cat /tmp/ab 
a
b
[root@localhost ~]#
</code></pre>
<p>（exit；）退出子shell</p>
<p>{exit；}退出当前shell</p>
***
<p>$SHLVL 这个变量 可以查看你当前shell跑到了第几层。</p>

举例：
<pre><code>
[root@localhost ~]# bash
[root@localhost ~]# bash
[root@localhost ~]# bash
[root@localhost ~]# bash
[root@localhost ~]# echo $SHLVL
5
[root@localhost ~]#
</code></pre>
<p>好处是在写shell脚本时，观察自己的变量在第几层执行。</p>
<p>总结shell的内置变量，总结常用。</p>
***
<p>| 管道 把前面执行的结果，交给后面的命令处理。</p>
<p>‘ ’强引用</p>
<p>“ ”弱引用</p>
举例：
<pre><code>
[root@localhost ~]# echo '$a'
$a
[root@localhost ~]# echo "$a"
test
[root@localhost ~]#
</code></pre>
***
<p>\ 转义字符。所谓的转义就是把字符本身的含义转化成另外一个含义。根据不同的环境进行转移。</p>
<p>`` 反引号，键盘1左边的那个反引号。 实际中shell在执行的过程中，先执行的 `` 里面的动作，再执行整个语句。在shell中不见意常用。</p>
举例：
<pre><code>
[root@localhost ~]# echo `whoami`
root
[root@localhost ~]# 
</code></pre>
<p>$( ) 和 ` ` 执行效果一样，在shell脚本中建议使用$()</p>
举例：
<pre><code>
[root@localhost ~]# echo $(whoami) 
root
[root@localhost ~]#
</code></pre>
***
<p>eval 把后面的字符串当命令来使用。</p>
举例：
<pre><code>
[root@localhost ~]# eval $(echo whoami) 
root
[root@localhost ~]# 
</code></pre>
***
<p>其他变量 type去查看</p>
<pre><code>
[root@localhost ~]# type /bin/echo 
/bin/echo is /bin/echo
[root@localhost ~]# type test
test is a shell builtin
[root@localhost ~]# 
</code></pre>
***

*	七． 数学运算与数组。
<p>复杂的数学运算使用bc，下面是简单的数学运算。</p>
<pre><code>
[root@localhost ~]# a=1
[root@localhost ~]# b=2
[root@localhost ~]# echo $a+$b
1+2
[root@localhost ~]# echo $[a+b]
3
[root@localhost ~]# echo $((a+b))
3
[root@localhost ~]# c=$a+$b
[root@localhost ~]# echo $((c))
3
[root@localhost ~]# 
</code></pre>

***
<p>数组：</p>
<p>定义数组：</p>
<p>`Var=（word0 word1 word2 word3）`</p>
<p>`echo ${var[0]}` 查看结果。</p>
<pre><code>
[root@localhost ~]# boss=(kk yy zz)
[root@localhost ~]# echo ${boss[0]}
kk
[root@localhost ~]# echo ${boss[2]}
zz
[root@localhost ~]# echo ${boss[1]}
yy
[root@localhost ~]# echo ${boss[*]}
kk yy zz
[root@localhost ~]# echo ${#boss[*]}
3
[root@localhost ~]# echo ${#boss[0]}
2
[root@localhost ~]# unset boss
[root@localhost ~]# echo ${#boss[*]}
0
[root@localhost ~]# boss=(kk yy zz) 
[root@localhost ~]# echo ${boss[*]} 
kk yy zz
[root@localhost ~]# 
</code></pre>
***
<p>改变数组中的元素。</p>
<pre><code>[root@localhost ~]# boss=(aa bb cc)
[root@localhost ~]# echo ${boss[*]}
aa bb cc
[root@localhost ~]# boss[1]=hello
[root@localhost ~]# echo ${boss[*]}
aa hello cc
[root@localhost ~]# 
</code></pre>
<p>举例: 数组相当于古代的翻牌子</p>
<p>妃子=（华妃 甄环）</p>
***










