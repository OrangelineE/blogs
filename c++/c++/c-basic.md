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