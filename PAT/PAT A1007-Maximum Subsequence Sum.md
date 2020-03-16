# PAT A1007-Maximum Subsequence Sum

## 题目

Given a sequence of *K* integers { *N*1, *N*2, ..., *N**K* }. A continuous subsequence is defined to be { *N**i*, *N**i*+1, ..., *N**j* } where 1≤*i*≤*j*≤*K*. The Maximum Subsequence is the continuous subsequence which has the largest sum of its elements. For example, given sequence { -2, 11, -4, 13, -5, -2 }, its maximum subsequence is { 11, -4, 13 } with the largest sum being 20.

Now you are supposed to find the largest sum, together with the first and the last numbers of the maximum subsequence.

### Input Specification:

Each input file contains one test case. Each case occupies two lines. The first line contains a positive integer *K* (≤10000). The second line contains *K* numbers, separated by a space.

### Output Specification:

For each test case, output in one line the largest sum, together with the first and the last numbers of the maximum subsequence. The numbers must be separated by one space, but there must be no extra space at the end of a line. In case that the maximum subsequence is not unique, output the one with the smallest indices *i* and *j* (as shown by the sample case). If all the *K* numbers are negative, then its maximum sum is defined to be 0, and you are supposed to output the first and the last numbers of the whole sequence.

### Sample Input:

```in
10
-10 1 2 3 4 -5 -23 3 7 -21
```

### Sample Output:

```out
10 1 4
```

## 解题思路

姥姥的数据结构正好有个例子就是这道题，思路就是从头开始找子串，如果一个连续的子串和为负数，那么这个子串不能为后面新添加的元素增加子串和，还不如把这个负的子串给去掉重新开始另一个子串，所以遇到子串和为负数时就把子串丢弃，子串头尾都移到负数子串的后一个元素然后开始一个新的子串，当遇到有子串的和大于记录着的最大子串和时进行更新，同时一并更新子串的起始于结束位置，一开始把记录最大子串和的max定义为-1，那么在全是负数的时候，最终max为-1，但凡不全是负数，那么max的值必然会大于等于0，所以通过这样设定很容易判断所给的数据是否全部为负数。

## 代码

```c
#include <stdio.h>

using namespace std;

int main() {
    int K;
    scanf("%d", &K);
    int max = -1, rstart = 0, rend = 0, sum = 0, num[K], start = 0, end = 0;
    for(int i = 0; i < K; i++){
        scanf("%d", &num[i]);
        sum += num[i];
        if(sum < 0){
            sum = 0;
            start = i + 1;
            end = i + 1;
        }
        else if(sum > max){
            rstart = start;
            rend = i;
            end = rend;
            max = sum;
        }else{
            end = i;
        }
    }
    if(max == -1){
        printf("0 %d %d\n", num[0], num[K-1]);
    }else{
        printf("%d %d %d\n", max, num[rstart], num[rend]);
    }
    return 0;
}

```

