# PAT A1010-Radix（二分法）

## 题干

Given a pair of positive integers, for example, 6 and 110, can this equation 6 = 110 be true? The answer is `yes`, if 6 is a decimal number and 110 is a binary number.

Now for any pair of positive integers *N*1 and *N*2, your task is to find the radix of one number while that of the other is given.

### Input Specification:

Each input file contains one test case. Each case occupies a line which contains 4 positive integers:

```
N1 N2 tag radix
```

Here `N1` and `N2` each has no more than 10 digits. A digit is less than its radix and is chosen from the set { 0-9, `a`-`z` } where 0-9 represent the decimal numbers 0-9, and `a`-`z` represent the decimal numbers 10-35. The last number `radix` is the radix of `N1` if `tag` is 1, or of `N2` if `tag` is 2.

### Output Specification:

For each test case, print in one line the radix of the other number so that the equation `N1` = `N2` is true. If the equation is impossible, print `Impossible`. If the solution is not unique, output the smallest possible radix.

### Sample Input 1:

```in
6 110 1 10
```

### Sample Output 1:

```out
2
```

### Sample Input 2:

```in
1 ab 1 2
```

### Sample Output 2:

```out
Impossible
```

大致就是给4个数第一个数是`N1`，第二个数是`N2`，第三个数`tag`指明前面`N1`，`N2`其中一个数，最后的`radix`是基数，表示`tag`指明的数的基数是多少。

有了以上数据，就需要找到一个最小的基，使得`N1`=`N2`成立。

以上面的例子来说，`N1 = 6`，`N2 = 110`，`tag = 1`，`radix = 10`，到这里说明`N1`是以10为基的数，也就是一个十进制数，所以经过计算发现在`N2`为2进制数的时候`N1`与`N2`的值相同。所以输出为2。

## 解题思路

本题要涉及到比较，而此处的`N1`与`N2`可能会涉及到不同的进制数，所以我们把N1与N2都转化为十进制数来进行比较。`N1`很容易就转换成十进制数了，对于`N2`我们需要遍历多个基数`radix`，计算对应基数下，`N2`的十进制数值，再与`N1`比较，如果相等，那么就可能是我们要找的`radix`。

然后，由于`radix`需要多次遍历得出，所以这里使用二分法。那么首先得确定边界值，下边界`left`很容易确定，根据题干“`A digit is less than its radix and is chosen from the set`”给出的信息只需要找到`N2`中值最大的`digit`——max，再就可以求到`left = max + 1`，对于上边界`right`，有两种办法：1、`left`累乘2，可以知道在很少的循环次数后可以到一个非常大的数。在前一个数作为基数计算出来`N2`的十进制值小于`N1`，而当前这个数作为基数计算出的`N2`的十进制值大于`N2`时，可以知道，上界找到了，选择当前这个数作为`right`即可。2、还有一种办法是直接以`N1`计算出来的十进制值加一作为上界`right`。这样做的依据是：`right`决定了基数的上界，而`基数-1`是每一位`digit`的上界，这样取正好是个临界，如果设定的`right`小于这个值，那么当`N2`只有一位的时候，尽管这一位的值已经是可以等于`N1`十进制计算结果的，但是被上界所限制，不会得出正确的答案。如果`N2`有多位，那么这个值在非末位处的非零值，可以使得在`right`处取`radix`时得到的十进制结果必然是大于`N1`的，这样做保证了如果存在正确的`radix`必然包含在我们设定的区间之内。

基本思路有了，然后，里面非常重要的点就是，整型变量要选大一点，因为运算的数据非常大，其中测试点10，多次提交都无法通过，最后是修改了`long long`为 `unsigned long long`。

## 代码

```c++
#include <stdio.h>
#include <string.h>

using namespace std;
// 把字符串转化为十进制数
unsigned long long int stod(char snum){
    if(snum <= '9' && snum >= '0') return snum - '0';
    return snum - 'a' + 10;
}
// 计算数字N的十进制数值
unsigned long long int compute(int len, char *N, unsigned long long int radix){
    unsigned long long int sum = 0;
    for(int i = 0; i < len; i++){
        sum = sum * radix + stod(N[i]);
    }
    return sum;
}

int main() {
    char N[2][11];
    int tag, len[2];
    unsigned long long int right, left = 0, mid, radix, sum, osum = 0, theother;

    // 读取数据
    scanf("%s %s %d %lld",N[0], N[1], &tag, &radix);
    len[0] = (int)strlen(N[0]);
    len[1] = (int)strlen(N[1]);
    
  	//N1-0；N2-1于是当tag=1->N1，另一个就是theother = 2-1 =1->N2
    theother = 2-tag;
    tag = tag - 1;

    sum = compute(len[tag], N[tag], radix);// 计算tag指定的目标字符串代表的数的十进制值
    for(int i = 0; i < len[theother]; i++){// 找到另一个字符串的最大值
        if(left < stod(N[theother][i])) left = stod(N[theother][i]);
    }
  	// 边界值确定
    left += 1;
    right = sum + 1;
    
    while (left <= right){
        mid = (left + right) / 2;
        osum = compute(len[theother], N[theother], mid);
        if(osum > sum){
            right = mid - 1;
        }
        else if(osum < sum){
            left = mid + 1;
        }
        else if(osum == sum){
            printf("%lld", mid);
            return 0;
        }
    }
    printf("Impossible\n");
    
    return 0;
}

```

