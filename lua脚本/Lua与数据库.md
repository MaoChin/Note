# `Lua`与数据库

`lua`官方没有提供操作数据库的包，需要自己安装第三方库。

第三方库管理工具：`luarocks`

```shell
# 安装lua库管理工具
sudo apt install luarocks     
# 使用luarocks安装luasql-mysql
# MYSQL_DIR 表示本地mysql的安装路径，MYSQL_INCDIR表示mysql.h的路径
sudo luarocks install luasql-mysql MYSQL_DIR=/var/lib/mysql 			         MYSQL_INCDIR = /usr/include/mysql
# 使用luarocks安装redis-lua
sudo luarocks install redis-lua
```

## 1. `Lua`操作`MySQL`

```lua
-- 引入包
local luasql = require("luasql.mysql");

-- 建立句柄
client = luasql.mysql();
-- print(client);

-- 创建链接          数据库名  用户名 密码   IP          PORT
conn = client:connect("test_db", "root", "", "127.0.0.1", "3306");
if conn == nil then
  print("连接失败");
  os.exit();
end                                                                                        
-- 执行sql语句
ret = conn:execute("select * from Stu");
-- 获取数据
row = ret:fetch({}, "a");
-- print(row.id, row.age, row.name, row.score);
while row do                    
  print(row.id, row.age, row.name, row.score);
  row = ret:fetch(row, "a");
end

ret = conn:execute("insert into Stu values ( ... )");
ret = conn:execute("delete from Stu where ...");
-- 关闭连接
conn:close();
-- 关闭客户端
client:close();
```

## 2. `Lua`操作`redis`



 

 



















