# PAT A1040-Longest Symmetric String

## 题目

Given a string, you are supposed to output the length of the longest symmetric sub-string. For example, given `Is PAT&TAP symmetric?`, the longest symmetric sub-string is `s PAT&TAP s`, hence you must output `11`.

### Input Specification:

Each input file contains one test case which gives a non-empty string of length no more than 1000.

### Output Specification:

For each test case, simply print the maximum length in a line.

### Sample Input:

```in
Is PAT&TAP symmetric?
```

### Sample Output:

```out
11
```

## 解题思路

对于这种对称字符串需要考虑两种情况，一是双数的情况，那么没有中间字符，前后两部分数量相等的字符是对称的，一种情况是单数情况，有中间字符，除开中间字符，前后两部分数量相等的字符是对称的。于是设置三个指针，表示开始，中间位置和结束位置。中间位置在整个数组之间遍历，然后中间位置每遍历到一个字符，start与end指针向两边移动直到start与end指向的字符不等为止然后记录子串长度，然后mid开始遍历下一个字符。注意，这里我用了两个循环来实现的，也可以用一个，但是思维难度就更大了，对于这种小数据，费力想了很好的算法也优化不了多少，不如就开两个循环，最长的一组数据4ms完成。

## 代码

```c
#include <stdio.h>
#include <string.h>
int main() {
    char str[1010];
    scanf("%[^\n]", str);
    int Maxlen = 1, len = (int)strlen(str), Start = 0, Mid = 0, End = 0, Sublen = 0;
    for(Mid = 1; Mid < len-1; Mid++){
        Sublen = 0;
        for(Start = Mid-1, End = Mid+1; Start >= 0 && End < len;){
            if(str[Start] != str[End]) break;
            Sublen += 2;
            Start -= 1;
            End += 1;
        }
        Sublen += 1;
        if(Maxlen < Sublen) Maxlen = Sublen;
    }
    for(Mid = 1; Mid < len; Mid++){
        Sublen = 0;
        for(Start = Mid-1, End = Mid; Start >= 0 && End < len;){
            if(str[Start] != str[End]) break;
            Sublen += 2;
            Start -= 1;
            End += 1;
        }
        if(Maxlen < Sublen) Maxlen = Sublen;
    }
    printf("%d\n", Maxlen);
    return 0;
}

```

<img src="/Users/liyongyang/Documents/考研/PAT/img/3.png" alt="3" style="zoom:50%;" />