# `Lua`面向对象

## 1. `self`(类似`this`指针)

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
  obj = obj or {};   -- 设置默认值
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

其实就是设置多层`setmetatable`。

```lua
grandfather = {id = 1};
grandfather.__index = grandfather;

father = {name = "aaa"};
father.__index = father;

son = {};
setmetatable(father, grandfather);
setmetatable(son, father);

print(son.id);  -- 1
```

多层继承中的构造函数实现：

```lua
grandfather = {id = 1, name = "aaa"};

function grandfather:new(obj)
  obj = obj or {}; 
  setmetatable(obj, self);
  -- 要设置自索引，这样每一层继承就都可以生效了
  self.__index = self;
  return obj;
end
father = grandfather:new({id = 2});
son = father:new({});

print(grandfather.id);   -- 1
print(father.id);  			 -- 2
print(father.name);      -- aaa
print(son.id); 					 -- 1
```

## 5. 实现多态(重写/覆盖)

这一块很简单！就是一个就近原则。

```lua
father = {id = 1};
function father:Print()
  print("aaa");
end
function father:new(obj)
  obj = obj or {}; 
  setmetatable(obj, self);
  -- 要设置自索引，这样每一层继承就都可以生效了
  self.__index = self;
  return obj;
end

son = father:new({id = 2});
son.Print();   -- aaa

function son:Print()
  print("bbb")
end
son.Print();   -- bbb
```

## 6. 成员私有化

简单来说，就是不直接创建`table`，而是写一个`function`，在`function`中有变量，有方法。然后这个`function`返回一个`table`！这样外部就不能直接访问变量了。

```lua
-- 更严谨的写法
function getTable()
  -- 加上 local
  local member = {
    id = 1;
    name = "aaa";
  };

  local function getId()
    return member.id;
  end
  local function setId(newId)
    member.id = newId;
  end

  return {
    getId = getId;
    setId = setId;
  }
end
t1 = getTable();
print(t1.member); 			-- nil
print(t1.getId());			-- 1
t1.setId(2);
print(t1.getId()); 			-- 2
```



