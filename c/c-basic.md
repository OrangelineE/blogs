# C
C needs to be compiled. Therefore, it needs
* Editor
* Compiler
Or just IDE (Integrated Development Environment).
# Operator and Operand
## Operator has priority
1. `+`/`-` (positive/negative) e.g. a*-b will calculate -b first.
2. `*`/`/`
3. `+`/`-`
4. `=` e.g. a = b = 6 -> a = (b = 6)
## Prefix and Suffix
`++` and `--` can be put in both prefix and suffix. Is there any difference with these two positions?
Consider 2 cases:
```C
int a = 0;
printf("a++ = %d\n", a++);
printf("a = %d\n", a);

Terminal:
a++ = 0
a = 1
```
CASE 1

```C
int a = 0;
printf("++a = %d\n", ++a);
printf("a = %d\n", a);

Terminal:
++a = 1
a = 1
```
CASE 2

The same result is that both `a++` and `++a` will add a by 1, which results in `a = 1`.
The difference is that `a++` prints the result `before` adding 1 to a while `--a'

## Loop
+ If we know the start and end for the loop, use `for` loop
+ If it has to loop once, use `do-while`
+ Otherwise, use `while`

## string

+ 字符串以数组的形式存在，以数组或指针的形式访问，我们可以用指针访问一个数组，也可以用数组的形式去访问指针所代表的那一片连续的地址空间（但字符串在内存当中的表达形式一定是数组）。更多是以指针的形式。

+ 字符串可以表达为 char* 的形式，char* 不一定是字符串; 
  + 本意是指向字符的指针，可能指向的是字符的数组（就像 int* 一样）
  + 只有它所指的字符串数组有结尾的 0，才能说它所指的是字符串