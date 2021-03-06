# Linux socket 即时通讯系统
## 数据规范

### 数据格式

{“type”:”1”,”from”:”发送方”,”pwd”:”用户密码”,”to”:[“接收方1”,“接收方2”],”msg”:”发送信息”}

type：发送类型 0 登录，1 发送，2 注册
from：信息发起人
pwd：用户密码
to：信息接收人，可以为数组，表示群发信息
msg：发送信息内容

### 使用插件库

cJSON：C语言解析 json 数据

### 定义辅助函数

iUtil：辅助服务端解析转发消息

## 系统文件列表

server.c 服务端代码，函数入口
client.c 客户端代码，函数入口
cJSON.h C语言下实现 json 数据序列化和解析的方法库
cJSON.c cJSON.h方法的实现
util.h 自定义服务端扩展方法，iUtil 类库
util.c util.h方法实现
ALLUSERS.JSON 当前系统注册用户的信息，替代数据库功能
PRINTLOG.LOG 服务端打印日志记录状态

## 程序启动

### 服务器端编译

​ gcc server.c cJSON.c util.c -lm -o server

### 编译时添加自定义函数库、系统函数库
cJSON.c util.c 引用外部函数
-lm 引用内部函数库 lib cmath
启动服务器 ./server

### 客户端编译

​ gcc client.c cJSON.c -lm -o client

### 编译时添加自定义函数库、系统函数库

cJSON.c 引用外部函数
-lm 引用内部函数库 lib cmath
启动客户端 ./client

## 注册登录

### 登录验证

{“type”:”0”,”from”:”user1”,”pwd”: ”pwd1"}

验证用户是否存在，查询 ALLUSERS.JSON
密码是否正确
登录时，密码在终端不回显，使用 getpass 函数

### 注册用户

{“type”:”2”,”from”:”kangkang”,”pwd”: ”kangkang"}

检测是否已存在用户，查询 ALLUSERS.JSON
在服务器注册用户
注册时，密码在终端不回显，使用 getpass 函数

## 服务器转发信息

### 服务器接收消息类型标识

type：x 加载在线好友列表
type：0 用户登录
type：1 用户客户端之间发送消息
type：2 用户注册
type：3，4，5，6，7，8，9 服务端返回消息的不同类型，详见 util.c

### 接收登录信息

接收登录标识 type ：0
查询用户列表，是否已注册
查询在线列表，是否已登录
登录用户，更新在线列表

### 客户端之间转发消息

接收转发标识 type ：1
查询在线列表，接收方不在线则不发送
接收方为多个用户时，转发给每个用户
在服务端打印日志文件

### 接收注册信息

接收注册标识 type ：2
查询用户列表，是否账号已注册
为注册用户则添加用户列表，并重写用户文件 ALLUSERS.JSON


### 加载在线列表

接收列表标识 type ：x
查询在线好友
返回好友列表数组


