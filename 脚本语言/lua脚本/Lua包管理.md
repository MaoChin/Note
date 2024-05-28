# `Lua`包管理

一个包中定义一个类以及他的方法，然后在主程序中引入这些包即可。

两个文件不能相互`require`。

```lua
-- 有一个叫做 util.lua 的文件，里面写了类和方法
-- require这个文件(不用后缀！)，这样这个文件就可以使用util文件里的方法了
require("./util");

-- ...
```

## 1. 避免命名冲突

```lua
-- util.lua
local util = {};    -- 加 local
util.Method1 = function()
  -- ...
 end
-- ...

return util;  -- 类,只能返回一个！！
```

```lua
-- main.lua
-- 加 local
local tmpUtil = require("./util");
-- ...
```

## 2. 路径问题

`require`绝对路径会有问题？？需要用下面的`path`引入。

## 3. `path` && `cpath`

```lua
-- 所有默认路径，require时会去这些路径下找
package.path;
-- 找 .so 的默认路径
package.cpath;
```

当需要的文件在其他目录下时要设置这个环境变量。

```lua
package.path = package.path.."/tmp/?.lua";
```

## 4. `loaded  ` && `preload`

`loaded`表示当前文件会加载的库文件。

`preload`表示当前文件会预加载的库文件
