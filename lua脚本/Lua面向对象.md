# `Lua`面向对象

## 1. `self`(类似智能指针)

```lua
t1 = {id = 1, name = "aaa"};
function t1:getName()
  return self.name;
end

tmp = t1:getName();
```

## 2. 自索引和元表---实现继承

简单来说就是自己索引自己。

```lua
t1 = {id = 1, name = "aaa"};
meta = { phone = 111 };
-- 自己索引自己
meta.__index = meta;
setmetatable(t1, meta);
--- ...
```

实现继承：

```lua
father = { id = 1 };
function father:FPrint()
  print("father");
end

son = { id = 2 };
function son:SPrint()
  print("son");
end

son1 = { id = 3 };
function son1:SPrint()
  print("son");
end

-- 报错
-- son:FPrint();
setmetatable(son, father);    -- 典型的继承
setmetatable(son1, father);
-- 自索引
father.__index = father;
son.FPrint();  -- 子类可以调用父类的方法了！
```

## 3. 实例化(构造函数)

```lua
t1 = {id = 1, name = "aaa"};
t1.__index = t1;  -- 自索引一下
function t1:new(obj)
  obj = obj or {};
  -- 元表，obj有值就用他自己的，否则就用self的！！
  setmetatable(obj, self);
  return obj;
end
t2 = t1:new();
t3 = t1:new({id = 2});
print(t2.id);   -- 1
print(t3.id);   -- 2

print(t3.name);  -- aaa
t3.name = "bbb";
print(t3.name);  --- bbb
```

## 4. 多层继承(祖父类 <- 父类 <- 子类)









