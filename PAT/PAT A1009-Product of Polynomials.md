# PAT A1009-Product of Polynomials

## 题目

This time, you are supposed to find *A*×*B* where *A* and *B* are two polynomials.

### Input Specification:

Each input file contains one test case. Each case occupies 2 lines, and each line contains the information of a polynomial:

*K* *N*1 *a**N*1 *N*2 *a**N*2 ... *N**K* *a**N**K*

where *K* is the number of nonzero terms in the polynomial, *N**i* and *a**N**i* (*i*=1,2,⋯,*K*) are the exponents and coefficients, respectively. It is given that 1≤*K*≤10, 0≤*N**K*<⋯<*N*2<*N*1≤1000.

### Output Specification:

For each test case you should output the product of *A* and *B* in one line, with the same format as the input. Notice that there must be **NO** extra space at the end of each line. Please be accurate up to 1 decimal place.

### Sample Input:

```in
2 1 2.4 0 3.2
2 2 1.5 1 0.5
```

### Sample Output:

```out
3 3 3.6 2 6.0 1 1.6
```

## 解题思路

用的map，键值存指数，值存系数，然后对应相乘即可。不过map默认是按照升序排列的，但是这道题的结果需要按键值的降序排列，所以要用到map里的一个参数greater`map<int, double, greater<int>> mp`，这样所键值大的会放在前面。

但是这道题用数组也是很方便能解出来的，所以又说明一个问题，能用数组最好用数组，第一个测试点存在最终结果`N=0`的情况，很明显这样的话，这一项是不能打出来的，最早一版的修改是在打印的时候设置一个判断为0不打印，结果依然通过不了，然后第二次，得到的存结果的最终map遍历一遍找到值为0的点mp.erase()，还是不能通过，我让我以为问题不在这，所以重复检查了几遍代码，最后再改了一次，是在相乘运算时新产生的系数加上之前算出来的系数如果为0，就把这个位置元素删除(就是现在这版代码)，结果通过了。其实有点迷，前面的思路明明跟这版没有差别呀，只是在不同的地方对这样的元素进行了阻止输出。反正用map的时候还是谨慎仔细一点吧。

## 代码

```c
#include <stdio.h>
#include <unordered_map>
#include <map>
#include <algorithm>

using namespace std;

bool cmp(unordered_map<int, double>::iterator a, unordered_map<int, double>::iterator b){
    return a->first > b->first;
    
}

int main(){
    unordered_map<int, double> A, B;
    map<int, double, greater<int>> R;
    int KA, KB;
    scanf("%d", &KA);
    int exp;
    double coe;
    for(int i = 0; i < KA; i++){
        scanf("%d %lf", &exp, &coe);
        A[exp] = coe;
    }
    scanf("%d", &KB);
    for(int i = 0; i < KB; i++){
        scanf("%d %lf", &exp, &coe);
        B[exp] = coe;
    }
    
    
    for(unordered_map<int, double>::iterator it = A.begin(); it != A.end(); it++){
        for(unordered_map<int, double>::iterator itB = B.begin(); itB != B.end(); itB++){
            coe = it->second * itB->second;
            exp = it->first + itB->first;
            R[exp] += coe;
            if(R[exp] == 0) R.erase(exp);
        }
    }
    unordered_map<int, double>::iterator it1, it2;
    printf("%d", (int)R.size());
    for(map<int, double>::iterator it = R.begin(); it != R.end(); it++){
        printf(" %d %.1lf", it->first, it->second);
    }
    printf("\n");
    return 0;
}

```

