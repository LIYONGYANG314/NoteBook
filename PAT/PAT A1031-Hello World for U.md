#PAT A1031-Hello World for U

## 题目

Given any string of *N* (≥5) characters, you are asked to form the characters into the shape of `U`. For example, `helloworld` can be printed as:

```
h  d
e  l
l  r
lowo


      
    
```

That is, the characters must be printed in the original order, starting top-down from the left vertical line with *n*1 characters, then left to right along the bottom line with *n*2 characters, and finally bottom-up along the vertical line with *n*3 characters. And more, we would like `U` to be as squared as possible -- that is, it must be satisfied that *n*1=*n*3=*m**a**x* { *k* | *k*≤*n*2 for all 3≤*n*2≤*N* } with *n*1+*n*2+*n*3−2=*N*.

### Input Specification:

Each input file contains one test case. Each case contains one string with no less than 5 and no more than 80 characters in a line. The string contains no white space.

### Output Specification:

For each test case, print the input string in the shape of U as specified in the description.

### Sample Input:

```in
helloworld!
```

### Sample Output:

```out
h   !
e   d
l   l
lowor
```

## 解题思路

照题目的意思n1和n3相等，他们的取值尽可能接近于n2，又n1+n2+n3-2=N所以很容易知道三边之和等于N+2说明拐角处的点被计算了两次。知道了他们的和那么对总和除三，表示先对三条边的字符数进行均分，余下的字符顶多为二所以肯定只能加到底边上去。那么两条竖边和一条底边长度都确定了就开始打印。很明显知道两条竖边的对应位置在字符串里对应着正数第n位和倒数第n位，那么一行的起始和结束是很容易一起打出来的，对于空格部分我们只需要申请一个字符数组，字符数组的长度由（底边字符串长度-2）+1确定，加一是为了存储\'\0\'，那么这个字符串用memset以空格填充，之后空格部分打印这个字符串就可以了。最终20行解决。

## 代码

```c
#include <stdio.h>
#include <string.h>
int main() {
    int len, vertical, horizontal;
    char str[100];
    scanf("%s", str);
    len = (int)strlen(str);
    vertical = (len+2) / 3;
    horizontal = len - 2 * vertical;
    char space[horizontal+1];
    memset(space, ' ', sizeof(space));
    space[horizontal] = '\0';
    for(int i = 0; i < vertical-1; i++){
        printf("%c%s%c\n", str[i], space, str[len-1-i]);
    }
    for(int i = vertical-1; i <= len-vertical; i++){
        printf("%c", str[i]);
    }
    printf("\n");
    return 0;
}
```

