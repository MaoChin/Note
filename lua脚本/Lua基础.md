# `Lua`基础

## 1. 变量命名规范

`Lua`是弱类型语言，即**动态类型语言，定义变量时不需要类型修饰。**

而且变量类型可以随时改变。

变量命名规范和C基本一样。

### 1. 变量类型

全局变量(默认的)，局部变量(用`local`关键字修饰)，表字段。

```lua
do 
	-- 这是作用域，类似C的 {}
  aa = 1;  				-- 默认是全局变量！
  local bb = 2;  	-- 这个才是局部变量
end

print(aa);	 -- 可以访问
print(bb);   -- 报错
```

## 2. 基本数据类型

`Lua`有8种基本数据类型：`nil(空类型)，boolean，number(包括整型和浮点型)，string(单引号，双引号都表示字符串)，function(函数类型)，userdata(自定义数据类型)，thread(协程)，table(数组/哈希)`。使用库函数`type()`可以查看变量类型。

##### `boolean`

==在`Lua`中，只有`false`和`nil`表示假！！0和空串表示真！==

##### `string`

转义字符和C一样。

```lua
str = "a\nsss\t";
str1 = [[a\nsss\t]];   -- 忽略转义字符
```

##### `function`

`Lua`的`function`很灵活。可以作为参数被传递，也可以作为类型。

```lua
function func1(a, b)
  -- ...
  return a, 123;
end
-- 匿名函数
ret = function(a, b)
  return a + b;
end
sum = ret(1, 2);
```

##### `table`(可以当数组，也可以当哈希，还可以混着写！)

==`table`当数组时下标从1开始！！==

```lua
-- key = value， 增删改查用key(字符串)
info={
  id = 12,
  name = "aaa",
  age = 99
};

-- 数组，增删改查用下标
info2 = {123, 11.22, "sas"};

-- 混着写
info3 = {
  123,
  id = 12,
  name = "sasa",
  {"asa", age = 2},
  xxx = {99.11, aa = 12}
}
```

另外在`lua`中，认为`nil`是无效的，所以如果`nil`在`table`的最后，会直接忽略掉。

```lua
t1 = {1,2,3,nil,nil};
count = #t1;  -- 3(后面两个nil直接扔了)
t1 = {1,2,3,nil,nil,6};
count = #t1;  -- 6
```

## 3. 基本运算符

#### 1. 赋值

支持多重赋值：

```lua
a,b,c = 1, "aaa", false;
-- 交换swap
a, b = b, a;
```

算数运算符，关系运算符和C差不多。

注意：

1. 支持指数运算：`^`(在C中这是按位异或)
2. 不等于：`~=`(C中是 `!=`)
3. ==关系运算符只能在相同的数据类型之间使用！(不会做隐式类型转换)==
4. 对于对象类型的数据(`function，userdata，table`)，比较的是地址。

#### 2. 逻辑运算符

`and `  -> `&&`，`or`  ->  `||`，`not`  -> `!`

==并且`Lua`的逻辑运算符返回的是参与运算的变量之一==！！(其他语言都是返回的`true/false`)

`a and b`：若`a` 为真则返回`b`，否则返回`a`(不管`b`)。

`a or b`：若`a`为真则返回`a`(不管`b`)，否则返回`b`。

`not a`：这个和C一样，返回`true/false`。

以上规则称为==短路运算(短路规则)。==

短路运算的应用：设置默认值，实现三目运算

```lua
-- 设置a的默认值为100
a = a or 100;
-- 三目运算
d = ((a and {b}) or {c})[1];
```

## 4. 流程控制

#### 1. 分支

```lua
if ... then
  -- ...
elseif ... then
  -- ...
else 
  -- ...
end
```

#### 2. 循环(有`break`，没有`continue`)

```lua
-- for
-- pairs/ipairs：迭代函数
for k,v in pairs(table) do
  -- ...
end
for i = 1,10 do  -- i=1,10,2 i每次+2
  -- i每次+1(默认值)
end

-- while
while i < 100 do    -- 条件不成立结束循环
  i = i + 1;
  if i > 50 then
    break;
  end
  if i > 80 then
    goto FLAG;    -- 跳转
  end
end
::FLAG::  -- FLAG具有局部属性！！作用域内的FLAG在作用域外不可见

-- repeat(有点像do ... while，但不完全一样)
repeat
  i = i + 1;
  ...
until i >= 100; -- 条件成立结束循环
```

迭代函数`ipairs`和`pairs`：

1. `ipairs`：==顺序遍历==，**遇到`table`中的哈希类型时跳过！遇到第一个`nil`就直接终止，跳出循环。**主要用于==数组类型==的`table`遍历。
2. `pairs`：遇到`nil`就跳过，**既可以遍历数组类型，也可以遍历哈希类型**，混搭也可以遍历！所以适用范围更广。

