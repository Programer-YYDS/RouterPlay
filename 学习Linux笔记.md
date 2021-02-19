# 学习Linux笔记

## 简介

1.  Linu发行家族:Debian Fedora Suse 其他发行家族
2. 宝塔面板:Bt-Panel: http://39.98.121.54:8888/21376444
   username: phjzpulb
   password: c26f5e04
3. 系统启动过程
4. 主要目录介绍
   - /etc : 存放所有系统管理所需要的文件和目录
   - /home: 用户的主目录,一般目录名是以用户的账号命名的
   - /lib:Linux库就像Windows里面的的.dll 共享库
   - /root:超级目录
   - /usr:用户应用程序还有文件都放在这个目录下 类似于 program files
   - /var:日志文件

## 文件属性

1. 文件属性

   - [b]:表示目录

   - [-]:表示文件

   - [l]:表示链接文档

   - [b]:表示装置文件里面可供存储的接口设备

   - [c]:表示装置文件的串行端口设备

2. 权限

   ![363003_1227493859FdXT](https://www.runoob.com/wp-content/uploads/2014/06/363003_1227493859FdXT.png)

3. 更改文件属性

   ```shell
   chgrp [-R] 属组名 文件名  //-R属性递归更改文件属组,整个目录会更改
   chown [-R] 属主名 文件名  //更改属组名
   chown [-R] 属主名:属组名 文件名 //同时更改属组名 和 属主名
   chmod [-R] xyz 文件或者目录 ###通过数字更改权限，权限分数: r:4 w:2 x:1
   chmod [-R] u=...,g=...， o=..., 文件名 ###直接填写属性名字 a为all
   ```

## 文件目录管理

```shell
pwd -P（显示出确实的路径）#显示当前目录
cd #切换目录
ls -a(隐藏文件显示参数) -d（仅列出目录本身）-l(列出详细目录)
mkdir -[mp]  #m参数是指定属性名字 p参数指定递归创建  创建目录
rmdir [-p] # 删除空的目录 p参数：递归删除 连同上一级目录也删除
cp [-adfilprsu] 来源档 目标档 #复制文件
rm -[fir] 文件或者目录 # 参数f：忽略不存在的文件，不存在警告信息 参数r：递归删除
mv -[fiu] 来源 目标 #移动文件目录，或者修改名称 
```

## Linux用户和用户组管理

1. 系统用户账号的管理

   ```shell
   useradd 选项 用户名  #参数 -c:一段注释描述 -g:所属用户组 -d:用户目录 -s:登录的shell程序 -u:用户号
   userdel -r 用户名 #删除用户 参数r:连同目录一起删除
   usermod 选项 用户名 #修改账号
   su username #更改用户
   ```

2. 用户组的管理

   实际上就是对etc/group文件的更新

   ```shell
   groupadd 选项 用户组 #参数g:用户组标识号码 增加新的用户组
   groupdel 用户组 # 删除用户组
   groupmod 选项 用户组 #参数 g: 新的组标识号 -n新的组名字 
   newgrp root #一个用户同时在多个组里面 可以使用此命令切换组别
   passwd 选项 用户名 #用户口令管理 参数 l:锁定口令禁用帐号 u：口令解锁 d：使帐号无口令 -f：强迫用户下次登录修改口令
   ```

3. 与用户账号相关的系统文件

   1. /etc/passwd文件是用户管理工作的一个文件打开它显示

      ```
      tanyongfeng:x:1002:1002::/home/tanyongfeng:/bin/bash
      ```

      其中每一行都对应一个用户,每行记录又被冒号分割为7个字段 具体含义为

      ```txt
      用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
      ```

      1. 用户名:长度<8个字符,由大小写还有数字以及/组成

      2. 口令:为了加密,密码在这个文件中显示特殊字符"x".真正加密后的密码在/etc/passwd文件中

      3. 用户标识:是一个整数代表一个用户名(如果几个用户的标识符号一样,系统把他们看作是一个账户,但是口令等不同)  0是 超级用户的root的标识符 1~99系统保留,普通用户从100开始最大为500

      4. 组标识符号:对应/etc/group中的每一条记录

      5. 注解性描述:记录着一些个人情况 可以用作finger命令输出

      6. 主目录: 用户的起始工作目录

      7. 登录shell:用户登录后，要启动一个进程，负责将用户的操作传给内核，这个进程是用户登录到系统后运行的命令解释器或某个特定的程序，即Shell。

      8. 系统中有一类用户成为伪用户:有记录但是不能登录 因为shell为空 为了方便管理 常见伪用户

         ```
         bin 拥有可执行的用户命令文件 
         sys 拥有系统文件 
         adm 拥有帐户文件 
         uucp UUCP使用 
         lp lp或lpd子系统使用 
         nobody NFS使用
         ```

         

   2. /etc/shadow文件是经过pwconv命令将/etc/passwd文件生成的,只有超级用户才拥有该文件的权限.

      ```
      mysql:!!:18449:0:99999:7:::
      登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志
      ```

      1. 登录名:与用户名一致
      2. "口令"字段存放的是加密后的用户口令字，长度为13个字符。如果为空，则对应用户没有口令，登录时不需要口令；如果含有不属于集合 { ./0-9A-Za-z }中的字符，则对应的用户不能登录。
      3. 最后一次修改时间:1970年1月1日距离上次修改口令的时间
      4. "最小时间间隔"指的是两次修改口令之间所需的最小天数
      5. "最大时间间隔"指的是口令保持有效的最大天数。
      6. "警告时间"字段表示的是从系统开始警告用户到用户密码正式失效之间的天数。
      7. "不活动时间"表示的是用户没有登录活动但账号仍能保持有效的最大天数。
      8. "失效时间"字段给出的是一个绝对的天数，如果使用了这个字段，那么就给出相应账号的生存期。期满后，该账号就不再是一个合法的账号，也就不能再用来登录了。

   3. 用户组文件

      用户组信息文件保存在 /etc/group文件中

      ```
      tan4:x:102:
      组名:口令:组标识号:组内用户列表
      ```

   4. 批量增加用户方法

      1. 按照passwd密码格式新建密码文件

         ```shell
         us001:X:1000:1000::/home/us001:sbin/bash
         us002:X:1001:1001::/home/us002:sbin/bash
         #保存为user.txt
         ```

      2. 运行newusers命令，从刚创建的文件中导入数据，创建用户：

         ```shell
         newusers <user.txt
         ```

      3. 创建一个空口令文件内容格式**用户名：口令**

         ```shell
         us001:123456
         us002:123456
         #保存为pass.txt
         ```

      4. 运行chpasswd命令

         ```shell
         chpasswd <pass.txt
         ```

      5. 可以执行命令 `vipw` 或 `vi /etc/passwd` 检查 `/etc/passwd` 文件是否已经出现这些用户的数据，并且用户的宿主目录是否已经创建


## Linux磁盘管理

1.  常用命令

   ```shell
   df #列出文件系统的整体的性能问题
   du #检查磁盘空间使用量
   fdisk #用于磁盘分区
   ```

2. 命令详解

   1. df命令

      功能：检查磁盘空间占用量

      ```shell
      df [-ahikHtm] [目录或者文件名]
       #-a:列出所有的文件系统，包括特有的 /proc等文件系统
       #-k -m -h:分别以KBytes，MBytes以及自动方式进行显示
       #-H:以 M=1000K 取代 M=1024K 的进位方式；
      ```

   2. du命令

      功能：对文件和目录磁盘使用情况的查看

      ```shell
      du [-ahskm] 文件或目录名称
       #-a:列出所有的文件与目录容量 
       #-h :同上
       #s：列出总量
       # -k,-m  
      ```

   3. fdisk

      功能：fdisk是Linux的磁盘分区表操作

      ```
      fdisk [-l] 装置名称
      ```

   4. 磁盘格式化

      ```shell
      mkfs [-t 文件系统格式] 装置文件名 #参数 -可以接文件系统格式
      ```

## yum

1. yum（ Yellow dog Updater, Modified）是一个在Fedora和RedHat以及SUSE中的Shell前端软件包管理器。

2. yum语法

   ```shell
   yum [options] [command] [package ...]
   参数:  options:可选 -h:帮助 -y安装过程选择全为yes -q：不显示安装过程
   command :要进行的操作
   package:操作的对象
   ```

3. 常用命令

   ```
   列出需要更新的软件清单：yum check-update
   更新所有的软件命令： yum update
   仅安装指定的软件命令： yum install <package_name>
   仅更新指定的软件命令： yun update <package_name>
   列出所有可以安装的软件 ： yum list 
   删除软件命令 ： yum remove <package_name> 
   查找软件包命令： yum search <keyword>
   清除缓存命令: yum clean packages;清除缓存目录下的软件包
   			yun clean headers : 清除缓存目录下的headers
   			yum clean oldheaders : 清除缓存目录下旧的headers
   			yum clean / yum clean all :清除缓存目录下的软件包以及旧的headers
   ```

## Shell编程

1.  shell脚本是一个应用程序，提供通过界面访问系统内核的服务。

2. 编写shell

   1. shell运行方法

      ```shell
      1.编写shell脚本，保存为test.sh 
      #！/bin/sh   #！是一个约定的标记，告诉系统要用什么脚本需要什么解释器执行，使用那一种Shell
      echo "hello world!"   
      2.运行shell脚本
      chmod 777 ./test.sh 
      chmod +x ./test.sh     #增加运行权限
      ./test.sh  #作为可执行程序运行脚本 文件前面必须加 /bin/sh
      /bin/sh test.sh #作为解释器运行参数 前面不用加 #!/bin/sh
      #注意运行时，前面不能“./”应为没有的话，他回去PATH路径下去照 如果文件不在PATH路径下就会报错。
      ```

   2. Shell变量

      ```shell
      1.定义shell变量
      	your_name="tanyongfeng"
              #变量名和等号之间不能有空格！
              #命名只能使用英文字母，数组和下划线。首个字母不能以数字开头
              #中间可以有下划线 不能有空格
              #关键字问题
      2.使用变量
      	$your_name
      	${your_name}
      		#使用一个变量只需要在前面加空格就行
      		#变量名外部的花括号是可选的		
      3.只读变量
      	$my_name="tanyongfeng"
      	readonly my_name
      		#使用readonly命令可以将变量定义为只读变量，只读变量不能改变
      4.删除变量
      	unset my_name 
      		#删除变量
      5.变量类型：局部变量 环境变量（全局变量） shell变量
      
      		
      ```

   3. Shell字符串

      ```shell
      1.字符串定义
      	Your_name="pig"
      	str1='You are $Your_name'
      	str2="You are \“$Your_name\”"
      		#单引号中间不能加变量 双引号中间可加变量
      		#双引号可以出现转移字符
      2.获取字符串长度 
      	echo ${#Your_name} #输出3
      3.提取子字符串
      	echo ${Your_name:1:2} #输出pi
      4.查找子字符串
      	echo `expr index "$Your_name" ig` #查找i或者g的位置（哪个字母先出现就先计算哪个）
      5.字符串截取
      	var=http://tanyongfeng.xyz/123.html
      	echo ${#*//} #删除从左开始第一个//之前的字符 输出 tanyongfeng 
      ```

   4. Shell数组

      ```shell
      #bash支持一维数组（不知道多维数组），并咩有限定数组的大小。
      1.数组的定义
      	数组名=（值1 值2 值3...）
      	array_name=(value1 value2 value3) #一起赋值
      	array_name[1]=value1
      	array_name[3]=value3
      	....................#可以使用不连续的下标
      2.读取数组
      	${数组名[下标]} #获取下标为值的数
      	${数组名[@]} #获取全部元素
      	${数组名[*]} #获取全部元素
   	${#数组名[@/*]} #获取元素的长度 与获取字符串相同
      ```

      数组遍历方式
   
      ```shell
       #!/bin/bash
      my_arry=(a b "c","d" abc)
      echo "-------FOR循环遍历输出数组--------"
      for i in ${my_arry[@]};
      do
        echo $i
   done
      
   echo "-------::::WHILE循环输出 使用 let i++ 自增:::::---------"
      j=0
   while [ $j -lt ${#my_arry[@]} ]
      do
        echo ${my_arry[$j]}
        let j++
     done
      
      echo "--------:::WHILE循环输出 使用 let  "n++ "自增: 多了双引号，其实不用也可以:::---------"
      n=0
      while [ $n -lt ${#my_arry[@]} ]
      do
        echo ${my_arry[$n]}
        let "n++"
      done
      
      echo "---------::::WHILE循环输出 使用 let m+=1 自增,这种写法其他编程中也常用::::----------"
      m=0
      while [ $m -lt ${#my_arry[@]} ]
      do
        echo ${my_arry[$m]}
        let m+=1
      done
      
      echo "-------::WHILE循环输出 使用 a=$[$a+1] 自增,个人觉得这种写法比较麻烦::::----------"
      a=0
      while [ $a -lt ${#my_arry[@]} ]
      do
       echo ${my_arry[$a]}
       a=$[$a+1]
      done
      ```
   
   5. Shell注释
   
      ```shell
      1.开头为#的行就是注释。
      2.多行注释
      	:<<EOF
      	注释行1
      	注释行2
      	...
      	EOF
      ```
   
   6. Shell传递参数
   
      当执行参数的时候，向脚本传递参数，脚本内获取参数的格式为 $n 。其中n代表一个数字。
   
      ```shell
      特殊字符处理:
        echo $#   #输出脚本得参数个数
       echo $*   #输出一个包含所有参数得字符串
        echo $$   #输出脚本运行当前ID号
        echo $@	#输出一个包含所有参数的字符串
        #### $* 和 $@ 的区别 ##########
        体现在双引号中，在运行脚本时输入三个参数1、2、3 第一等价与"1 2 3" 而第二个等价于"1""2""3"
        在下面大代码中有所体现
        #!/bin/bash
          # author:菜鸟教程
          # url:www.runoob.com
      
          echo "-- \$* 演示 ---"
          for i in "$*"; do
              echo $i
          done
         ###输出1 2 3
      
          echo "-- \$@ 演示 ---"
          for i in "$@"; do
              echo $i
          done
      	###输出   1  回车  2   回车  3
      ```
      
   7. Shell基本运算符
   
      算术运算符
   
      ```shell
      val=`expr 2 + 2`
      echo "两数之和为 : $val"
      ###注意###
      	1.表达式和运算符之间要有空格
      	2.表达式要被``包含，不是单引号
      其他运算符：
      echo `var1 \* var2` #当变量 做乘积时候 *前面必须加\
      ```
   
      逻辑运算符
   
      ```shell
      -eq #检测两个数是否相等
      -ne #检测两个数是否不等
      -gt #检测左边的数是否大于右边的
      -lt #检测左边的数是否小于右边的
      -gt #检测左边的数是否大于等于左边的 
      -le #检测左边的数是否小于等于左边的
      
      ###逻辑运算符只支持数字，不支持字符串，除非字符串为数字 
      ```
   
      布尔运算符
   
      ```shell
      ！ #非运算  表达式为true返回false
      -o #或运算
      -a #与运算
      ```
   
      逻辑运算符
   
      ```
      
      ```
   
      