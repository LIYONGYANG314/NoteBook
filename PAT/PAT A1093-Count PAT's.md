# PAT A1093-Count PAT's

## 题目

The string `APPAPT` contains two `PAT`'s as substrings. The first one is formed by the 2nd, the 4th, and the 6th characters, and the second one is formed by the 3rd, the 4th, and the 6th characters.

Now given any string, you are supposed to tell the number of `PAT`'s contained in the string.

### Input Specification:

Each input file contains one test case. For each case, there is only one line giving a string of no more than 105 characters containing only `P`, `A`, or `T`.

### Output Specification:

For each test case, print in one line the number of `PAT`'s contained in the string. Since the result may be a huge number, you only have to output the result moded by 1000000007.

### Sample Input:

```in
APPAPT
```

### Sample Output:

```out
2
```

大意就是数PAT字串个数，特别注意最后一句==have to output the result moded by 1000000007==，一开始我忽略了，所以结果没有%1000000007，找了半天错，大意了。

## 解题思路

以`A`作为定点，确定`A`左边`P`的个数，以及`A`右边T的个数，于是可以确定出，在一个`A`位置处，可以组合成的`PAT`字符串个数。

## 代码

```c
#include <stdio.h>
#include <string.h>

using namespace std;

int main(){
    char c[1000001];
    scanf("%s", c);
    
    unsigned int res = 0, len, P = 0, T = 0;
    len = (unsigned int)strlen(c);
    for(int i = 0; i < len; i++){
        if(c[i] == 'T') T++;
    }
    for(int i = 0; i < len-1; i++){
        if(c[i] != 'A'){
            if(c[i] == 'P') P++;
            else if (c[i] == 'T') T--;
            continue;
        }
        res = (res + P * T)% 1000000007;
    }
    printf("%d\n", res);
    return 0;
}

```

