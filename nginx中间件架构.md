翻强工具：SwitchySharp 



1. Lua的基础语法

   - lua安装

     yum install lua

   - 运行

     ```
     lua
     
     lua ./test.lua
     
     ```

   - 注释

     - 行注释

     ```
     --[[
     块注释
     --]]
     ```

   - 变量

     ```
     a='alo\n123"'
     a="alo\n123\""
     a='\97lo\10\04923"'
     a=[[alo
     123"]]
     布尔类型只有nil和false是false，数字0啊，"空字符串（'\0'）都是true
     lua中变量如果没有特殊说明，全是全局变量
     ```

   - while循环语句

     ```lua
     sum = 0
     num = 1
     while num <= 100 do
       sum = sum + num
       num = num + 1
     end
     print("sum=",sum)
     
     lua没有++或是+=这样的操作
     ```

   - for循环语句

     ```lua
     sum = 0
     for i=1,100 do
       sum = sum + 1
     end
     ```

   - if-else判断语句

     ```lua
     if age==40 and sex == "Male" then
     	print("大于40的男人")
     elseif age > 60 and sex ~="Female" then
     	print("非女人而且大于60")
     else
     	local age = io.read()
     	print("Your age is"..age)
     end
     ~= 是不等于
     字符串的拼接操作符 ".."
     io库的分别从stdin和stdout读写的read和write函数
     ```

2. Nginx+lua环境

   - LuaJIT

   - ngx_devel_kit和lua-nginx-module

   - 重新编译Nginx

     http://www.imooc.com/article/19597

3. Nginx调用lua模块指令

   Nginx的可插拔模块化加载执行，共11个处理阶段

   | set_by_lua<br />set_by_lua_file         | 设置Nginx变量。可以实现复杂的赋值逻辑 |
   | --------------------------------------- | ------------------------------------- |
   | access_by_lua<br />access_by_lua_file   | 请求访问阶段处理，用于访问控制        |
   | content_by_lua<br />content_by_lua_file | 内容处理器，接收请求处理并输出响应    |

4. Nginx lua API

   | ngx.var                          | nginx变量                           |
   | -------------------------------- | ----------------------------------- |
   | access_by_lua access_by_lua_file | 获取请求头                          |
   | ngx.req.get_uri_args             | h获取url请求参数                    |
   | ngx.redirect                     | 重定向                              |
   | ngx.print                        | 输出响应内容体                      |
   | ngx.say                          | 同ngx.print，但是最后输出一个换行符 |
   | ngx.header                       | s输出响应头                         |
   | ...                              |                                     |

#### 架构

