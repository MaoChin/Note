# `Lua`文件操作

## 1. `open` && `close`

```lua
io.open(filename, mode);
io.close();
-- 打开文件
f1 = io.open("test.txt", "r+");
-- 把标准输入重定向到f1
io.input(f1);
-- 从f1中读
str = io.read(f1);
io.close(f1);
```

`mode`是打开方式，和C基本一样：

1. `r`，只读，文件必须存在。
2. `w`，只写，文件不存在就创建，存在的话会把文件内容清空。
3. `a`，追加 (只写)，文件不存在就创建，存在的话不会把文件内容清空。
4. `r+`，读写方式打开，文件必须存在。
5. `w+`，读写方式打开，文件不存在就创建，存在的话会把文件内容清空。
6. `a+`，追加(读写)。
7. `b`，二进制形式。

## 2. `input` && `output`

```lua
io.input(fd); -- 把标准输入重定向到fd
io.output(fd); -- 把标准输出重定向到fd
```

## 3. `read` && `write`

```lua
-- flag: 前面的*可以不加
-- *l : 读一行
-- *a : 读取所有内容
-- *n : 读取一个数字！
-- 数字 : 读取多少个字符
io.read(f1);
io.input(f1);
io.read("l");    -- f1:read("l");
io.read("*l");   -- f1:read("*l");
io.read(3);  -- 读取3个字符

-- 覆盖式写入 
io.write("aaa");
```

## 4. `seek`

设置文件指针的位置。

```lua
fd:seek();   -- 返回当前文件指针的位置
fd:seek("set"); 		-- 设置文件指针从头开始
fd:seek("set", 2);  -- 设置文件指针从最开始后的两个位置开始
fd:seek("cur");  		-- 设置文件指针从当前位置开始
fd:seek("cur", 2);  -- 设置文件指针从当前位置后的两个位置开始
fd:seek("end");  		-- 设置文件指针到最后
fd:seek("end", -2); -- 设置文件指针到倒数第二个位置
```

## 5. `lines`

迭代函数，依次获取每一行。

```lua
for str in fd:lines() do
  ...
end  
```

