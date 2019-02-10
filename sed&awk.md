#### 正则表达式

1. 单个字符
   - 特定字符
   - 字符范围[]
   - 任意字符 .
2. 重复字符
   - \* : 0-任意个
   - ? : 0-1
   - \+ :1-任意个
   - {n,m} n~m 个
3. 边界字符
   - 首字符 ^
   - 尾字符 $
4. 元字符
   - 单词分隔 \b
   - 任何字类字符 \w
   - 任何非字类字符 \W

#### sed

```flow
flow
st=>start: 文本或管道输入
op=>operation: 读入一行到模式空间
cond=>condition: sed命令处理?
e=>end: 输出到屏幕
st->op->cond
cond(yes)->e
cond(no)->op
```

- sed 一次处理一行内容
- sed 不改变文件内容（除非重定向）

1. sed格式

   - 命令行格式

     ```sh
     sed [options] 'command' file(s)
     ```

     options: -e;-n

     command: 行定位(正则) + sed命令(操作)

     eg:

     ```sh
     sed -n '/root/p'
     sed -e '10,20d' -e 's/false/true/g'
     ```

   - 脚本格式

     ```sh
     sed -f scriptfile file(s)
     ```

   - 基本操作命令

     -p (打印相关的行)， 和参数-n配合使用

     定位一行

     x; /pattern/

     ```sh
     nl passwd | sed -n '10p'
     ```

     定位几行

     x,y ; /pattern/,x;

     /pattern1/, /pattern2/

     x,y!

     ```sh
     nl passwd | sed -n '/news/,/mooc/p'
     ```

     定位间隔几行

     first~step

     ```sh
     nl passwd | sed -n '1~2p'
     ```

   - 基本操作命令(2)

     -a 新增行/ i 插入行

     -c 替换行

     -d 删除行

     ```sh
     nl passwd | sed '5a ==========='
     nl passed | sed '1,5i ============='
     删除空行
     sed '/^$/d' all/txt 
     找出error日志
     sed -n '/Error/p' fresh.log
     ```

   - 基本操作命令(3)

     -s 替换 ： 分隔符/,#等

     -g 全局 ：替换标志

     ```sh
     sed 's/false/true/' passwd
     sed 's/:/%/g' passwd
     找出ip
     ifconfig eth0 | sed -n '/inet /p' | sed 's/inet.*r://' | sed 's/B.*$//'
     ```

   - 高级操作命令(1)

      { } : 多个sed 命令， 用；分开

     ```sh
     nl passwd | sed '{20,30d;s/false/true/}'
     ```

     n : 读取下一个输入行（用下一个命令处理）

     ```sh
     nl passwd | sed '{p;n}'
     ```

     & ：替换固定字符串

     ```sh
     sed 's/^[a-z_-]\+/&  /' passwd
     ```

     大小写替换：元字符 \u\l\U\L

     ```sh
     sed 's/^[a-z_-]\+/\u&/' passwd
     txt 文件名换成大写
     ls *.txt | sed 's/^\w\+/\U&/'
     ```

     （） 

     ```sh
     获取passwd文件USER UID GID
     sed 's/\(^[a-z_-]\+\):x:\([0-9]\+\):\([0-9]\+\):.*$/USER:\1  UID\2  GID:\3/' passwd
     ```

   - 高级操作命令4

     \\( )\ : 替换某种(部分)字符串(\\1 ,\\2)

     ```sh
     获取eth0 ip
     ifconfig eth0 | sed -n '/inet /p' | sed 's/ine.*r:\([0-9]\+\) .*$/\1/'
     ```

   - 高级操作命令5

     r : 复制指定文件插入到匹配行

     w: 复制匹配行拷贝到指定文件里

     源文件目标文件互操作

      ```sh
     sed '1r 123.txt' abc.txt
     sed '1w abc.txt' 123.txt
      ```

     q : 退出sed

     ```sh
     nl passwd | sed '10q'
     nl passwd | sed '/false/q'
     ```


#### awk 

1. awk 处理方式

   - awk 一次处理一行内容

   - awk 对每行可以切片处理

     ```sh
     awk '{print $1}' //输出首个单词
     ```

   - 命令行格式

     ```sh
     awk [option] 'command' file(s)
     ```

     *command* : pattern {awk 操作命令}

     *pattern*：正则表达式;逻辑判断式

     *{awk 操作命令}* : 内置函数: print()  printf() getline...

     ​			     控制指令: if(){}else{}; while(){}...

   - 脚本格式

     ```sh
     awk -f awk-script-file file(s)
     ```

   - awk 内置变量

     $0 : 整个当前行

     $1 : 每行第一个字段

     $2 : 每行第二个字段 

   - awk 内置参数

     分割符： options：-F field-separator(默认为空格)

     ```sh
     awk -F ':' '{print $3}' /etc/passwd
     
     awk -F ':' '{print $1 " " $3}' /etc/passwd
     ```

     NR: 每行的记录号

     NF: 每字段的数量变量

     FILENAME: 正在处理的文件名

     ```sh
     awk -F ':' '{print "Line: "NR, "COL: "NF, "USER: " $1}' passwd
     awk -F ':' '{printf("Line:%3s COL:%s USER:%s\n",NR,NF, $1)}' passwd
     用户ID>100的
     awk -F ':' '{if($3>100) print "Line: " NR, "USER: " $1}' passwd
     Error的日期
     sed '/Error/p' free.log | awk '{print $1}'
     awk '/Error/{print $1}' passwd
     ```

   - awk逻辑

     ~， ！~ ：匹配正则表达式

     ==， !=, <, > :判断逻辑表达式

     ```sh
     awk -F ':' '$1~/^m.*/{print $1}' passwd
     awk -F ':' '$1!~/^m.*/{print $1}' passwd
     awk -F ':' '$3>100{print $1, $3}' passwd
     ```

   - awk 扩展格式

     BEGIN{print "start"} pattern{commands}END{print "end"}

     ```sh
     awk -F ':' 'BEGIN{print "Line COL USER"}{print NR, NF, $1}END{"----"FILENAME"-----------"}' passwd
     ```

   - awk进行逻辑处理

     ```sh
     统计当前文件下的size大小
     ls -l | awk 'BEGIN{size = 0}{size+=$5}END{print "size is:" size/1024/1024"M"}'
     统计passwd人数
     awk -F ':' 'BEGIN{count=0}$1!~/^$/{count++}END{print "count = "count}' passwd
     awk -F ':' 'BEGIN{count=0}{if($3>100) name[count++]=$1}END{for(i=0;i<count;i++) print i, name[i]}' passwd
     netstat -anp | awk '$6~/CONNECTED|LISTEN/{sum[$6]++}END{for (i in sum)  print i , sum[i]}'
     ```
