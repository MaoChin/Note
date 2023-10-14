# 一些C的小细节

## 1. 静态的变量只会初始化一次

静态变量是全局的，全局只有一个( 不会改变)，也只会初始化一次！

```C
void testStatic()
{
  // 这个a只会初始化一次
	static int a = 0;
  a++;
  printf("%d ", a);
}
int main()
{
  testStatic();
  testStatic();
	testStatic();	
  return 0;
}
// 输出结果： 1 2 3 4
```

