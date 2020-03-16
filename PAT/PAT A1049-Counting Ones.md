# PAT A1049-Counting Ones

## 题目

The task is simple: given any positive integer *N*, you are supposed to count the total number of 1's in the decimal form of the integers from 1 to *N*. For example, given *N* being 12, there are five 1's in 1, 10, 11, and 12.

### Input Specification:

Each input file contains one test case which gives the positive *N* (≤230).

### Output Specification:

For each test case, print the number of 1's in one line.

### Sample Input:

```in
12
```

### Sample Output:

```out
5
```

## 解题思路

比如现在得到一个`n`位数，因为我们要找的是那些小于目标数的所有值中含有的1的个数，那么我们可以知道，对于除开最高位剩下的`n-1`位中，这`n-1`的值是可以任意取的，在这个`n-1`位数里任意一种可能值都会比这个目标`n`位数小。那么先处理这`n-1`位，对于这`n-1`位，可能其中只有1位为1，也可能有2位......那么，对于这么些个情况来对应求1的个数，比如说有三位为1那么我们就能知道这样的排列组合是有$C^3_{n-1}$种可能，那么对于这$C_{n-1}^3$种情况里的一种排列，有多少种取值可能呢，这个取决于剩余位有多少种组合方式，很明显应该是$9^{n-1-3}$种，9代表着：0、2、3、4、5、6、7、8、9，在一位上可能取这九种数，一共有n-1-3还可以取，所以就是这么个值，于是，对于只有三位为1的情况可以算出来应该有$C_{n-1}^3\times 9^{n-1-3}$种情况，而每种情况包含3个1，所以一共有$C_{n-1}^3\times 9^{n-1-3}\times3$这么多个1。

我们知道能进行前面的排列组合前提在于我们排列的这些数是小于目标数的，因为我们没算最高位嘛，所以`n-1`位怎么排都不会比目标数大。现在求到1的个数是在很小的一个范围里求的，那能不能扩大这个范围呢，于是想到，还能扩大到比最高位小1的数的地方比如目标数的最高位为8，那么我可以把最高位取到7，那么最高位为7以及7以下的数时剩余的各位可以随便取值都不会比目标数大，那么这样做最高可以取到7999..9的地方，于是到这里基本可以确定的1的个数又变成了$C_{n-1}^3\times 9^{n-1-3}\times3\times8$，对应首位为(0、1、2、3、4、5、6、7)，到这里还没取完，考虑到首位为1的情况也就是1000....0到2000.....0之间的所有数都可以为答案贡献一个1所以这里1的总数要加上这最高位代表的权重1000....0。于是到此，我们把0-7999...9的1的个数都算出来了，剩下的就是目标数除去最高位的余数了，那么把前面这些写成一个函数，再用来处理这些余数，不断进行下去知道余数只有1位为止，就可以终止了。但是这里还有一点要注意，当最高位为1的时候，余数会为结果贡献1的，所以需要判断，如果最高位为1，那么结果1的数量会增加尾数这么多个。前面这些都做好了就可以得到最终的结果了。

不管对于这种算法，还是网上大家都在发的那种算法，理解起来都有些复杂，到实际测试的时候不如用暴力解，只有两个测试点通过不了，还是很划算的。

对于这种解法可能我描述得也不太清楚，但是精髓就一点，就是在小于目标数的范围内排列组合，递归地求解问题(代码中递归是以循环实现的)。

## 代码

```c
#include <stdio.h>
#include <string.h>
int Cnn(int u, int d) {
    int a = 1, b = 1;
    for(int i = u; i > 0; i--){
        b *= i;
        a *= d;
        d--;
    }
    return a/b;
}
unsigned int strtonum(char *N){
    unsigned int sum = 0;
    for(int i = 0; i < strlen(N); i++){
        sum = (sum * 10) + (N[i] - '0');
    }
    return sum;
}
unsigned int count(char *N){
    int len = (int)strlen(N);
    unsigned int sum = 1, ans = 0;
    for(int i = 1; i < len; i++){
        sum = Cnn(i, len-1);
        for(int j = i + 1; j < len; j++){
            sum *= 9;
        }
        ans += (i * sum);
    }
    sum = 1;
    for(int i = 0; i < len-1; i++){
        sum *= 10;
    }
    int topbit = N[0]-'0';
    ans *= (topbit);
    if(topbit > 1){
        ans += sum;
    }else{
        ans += (strtonum(N) - sum + 1);
    }
    return ans;
}
int main() {
    char N[11];
    scanf("%s", N);
    unsigned int ans = 0;
    if(N[(int)strlen(N)-1] != '0') ans++;
    for(int i = (int)strlen(N); i > 1; i--){
        if(N[0] != '0') ans += count(N);
        memcpy(N, N+1, i);
    }
    printf("%d\n", ans);
    return 0;
}
```

