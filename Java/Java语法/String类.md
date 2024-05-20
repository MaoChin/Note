# `String`类

`Java`的字符串是没有 '\0' 结尾的，C++的`string`类也是一样的。

## 1. 比较

```java
String str = "aa";
boolean ret1 = str.equals("aaa");
boolean ret2 = str.compareTo("aaa");
```

## 2. 查找

```java
String str = "aa";
String ret1 = str.charAt(1);
int ret2 = str.indexOf('a');
int ret3 = str.lastIndexOf('a');	
```

## 3. 转换

```java
String s = String.valueOf(1234);
String str = "aa";
String ret1 = str.toUpperCase(str);
String ret2 = str.toLowerCase("ASF");
```

## 4. 替换

```java
String str = "aa";
System.out.println(str.replaceAll("a", "_"));
System.out.println(str.replaceFirst("a", "_"));
```

## 5. 拆分

```java
String str = "name=zhangsan&age=18";
String[] result = str.split("&");
for (int i = 0; i < result.length; i++) {
	String[] temp = result[i].split("=");
	System.out.println(temp[0]+" = "+temp[1]);
}
```

## 6. `StringBuffer` && `StringBuilder`

由于`String`类被设计成不可修改的，所以当遇到需要大量修改的字符串时考虑使用`StringBuilder`类。

`StringBuffer`与`StringBuilder`大部分功能是相似的，其中，`StringBuffer`采用同步处理，属于线程安全操作；而`StringBuilder`未采用同步处理，属于线程不安全操作。  

