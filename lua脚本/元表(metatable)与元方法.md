# 元表(`metatable`)与元方法

==元表的本质还是一个表==，表中有自定义的计算规则，用这些规则可以实现表与表之间的运算。而这些规则都是以函数的方式写在元表中，所以又称为元方法。

元表的方法类似于==运算符重载==。有了元表后，可以自定义表与表之间的运算。**这个元表就相当于表与表之间的桥梁，借助元表自定义表与表之间的各种运算。**

## 1. 设置元表

```lua
t1 = {1,2,3};
t2 = {11,22,33};
metaT1 = {};
-- 将metaT1设置为t1和t2的元表
setmetatable(t1, metaT1);
setmetatable(t2, metaT1);

-- 重载t1和t2之间的加法
metaT1.__add = function(t1, t2)   -- __add对应的value是一个函数
  ...
end
-- 重载t1和t2之间的减法
metaT1.__sub = function(t1, t2)
  ...
end
-- 重载t1和t2之的判等运算符
metaT1.__eq = function(t1, t2)
  ...
end
-- ...
```

## 2. `__index`

可以通过元表的`__index`方法向元表中添加新元素，这时如果**本来的表里没有这个`key`，就会使用这个元素**。如果**本来的表里已经有这个`key`了，那还是使用他自己的。**

```lua
t1 = {id = 1, name = "aa"};
metaT1 = {};
-- 将metaT1设置为t1的元表
setmetatable(t1, metaT1);
-- 这时t1就会使用phone这个元素
metaT1.__index = {phone = "111"};      -- __index对应的value是一个表
metaT1.__index = function(t, key) .... end;  -- 也可以是一个函数
```

## 3. `__newindex`

```lua
t1 = {id = 1, name = "aa"};
t2 = {};
metaT1 = {};
-- 将metaT1设置为t1的元表
setmetatable(t1, metaT1);
-- 设置__newindex是t2
metaT1.__newindex = t2;
-- 此时这个phone在t2中！！！
t1.phone = "1111";
print(t1.phone);    -- nil
print(t2.phone);    -- 1111
```

