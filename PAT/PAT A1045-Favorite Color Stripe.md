#PAT A1045-Favorite Color Stripe

## 题目

Eva is trying to make her own color stripe out of a given one. She would like to keep only her favorite colors in her favorite order by cutting off those unwanted pieces and sewing the remaining parts together to form her favorite color stripe.

It is said that a normal human eye can distinguish about less than 200 different colors, so Eva's favorite colors are limited. However the original stripe could be very long, and Eva would like to have the remaining favorite stripe with the maximum length. So she needs your help to find her the best result.

Note that the solution might not be unique, but you only have to tell her the maximum length. For example, given a stripe of colors {2 2 4 1 5 5 6 3 1 1 5 6}. If Eva's favorite colors are given in her favorite order as {2 3 1 5 6}, then she has 4 possible best solutions {2 2 1 1 1 5 6}, {2 2 1 5 5 5 6}, {2 2 1 5 5 6 6}, and {2 2 3 1 1 5 6}.

### Input Specification:

Each input file contains one test case. For each case, the first line contains a positive integer *N* (≤200) which is the total number of colors involved (and hence the colors are numbered from 1 to *N*). Then the next line starts with a positive integer *M* (≤200) followed by *M* Eva's favorite color numbers given in her favorite order. Finally the third line starts with a positive integer *L* (≤104) which is the length of the given stripe, followed by *L* colors on the stripe. All the numbers in a line a separated by a space.

### Output Specification:

For each test case, simply print in a line the maximum length of Eva's favorite stripe.

### Sample Input:

```in
6
5 2 3 1 5 6
12 2 2 4 1 5 5 6 3 1 1 5 6
```

### Sample Output:

```out
7
```

## 解题思路

题目的大致意思就是，会提供代表颜色的一串数字，然后还会提供另一串数字表示得到的结果需要遵循的顺序（注意这里只是表示结果要遵循这个顺序，而不要求结果也要包含这串顺序数字的所有字符），最后通过从代表颜色的的数串中剔除个别数字，使得得到的结果数串有最大的长度即可，把这个长度输出来。

其实，这道题就是求最长升序子序列，只不过数据要先进行一下处理。顺序序列{2 3 1 5 6}，我们可以认为沿着序列他们有着逐渐变大的序号，所以我们把这个逐渐升高的序号作为权重存在一个名为Order的数组里，在获得数据的时候以顺序数组的数字值作为索引，以输入次序作为值存在Order里，那么在之后获得颜色数串{2 2 4 1 5 5 6 3 1 1 5 6}时，能很方便把颜色数字转换为输出次序，比如顺序序列中3的次序是2，那么在颜色序列里把3转换成2。有了这样的转换，那么按照顺序序列输出就理解为颜色序列转换后的序列找寻最大升序子序列的过程。

然后是算法，对于找寻最大升序子序列有两种算法，一种很直观，但是复杂度很大($O(n^2)$)：

首先定义一个数组为`A`用来存数字，再定义一个数组为`B`，用`B[i]`来存以`A[i]`作为结尾的子串里的最大升序子串的元素个数。那么`A`有`n`个所以需要遍历`n`次以`A`的某元素作为结尾。而在每一次遍历里要考虑查找最大子串数，所以每个循环里又需要遍历`i-1`次，最坏的情况就需要`n*n`。当为`B[i]`赋值时，要遍历`B[i]`前面的所有元素，看`A[i]`是否比`A[i]`前面的某些元素大，如果大或者等，表示可以在以这些元素的最大子串后面加1再形成一个升序子串，而遍历完`A[i]`前的所有元素找到一个最大的升序子串值，那么这个值就可以赋给`B[i]`

这种算法的时间开销比较大：

<img src="/Users/liyongyang/Documents/考研/PAT/img/4.png" alt="4" style="zoom:50%;" />

另外一种算法很快时间复杂度$O(nlogn)$：

同样开`A`、`B`两个数组，但是`B`用来存最大升序子串，思路就是在遍历`A`中每个元素，在`B`中找到大于`A[i]`的第一个元素并用`A[i]`替代即可，这么做的原因是对于那个刚刚大于`A[i]`的`B`中元素来说用一个更小的数来替换他能够保证让更多的数加入到`B`这个序列里面来，而替换不会影响其他数字已经形成的子串，首先对于被替换位置后面的元素替换进来的元素本来就比他们小，所以后面的哪些元素形成的子串不会被影响，该多长还是多长，而对于替换位置之前的哪些元素，本来就在他们后面替换的那更不会影响到他们形成的子串长度了。这做下来发现以`B`中任一元素作为结尾，以`B[0]`作为开头都是一个升序数串，所以遍历完后最后一个`B`中元素的索引加`1`，得到的就是最终最长升序子序列的长度。而每遍历一个`A[i]`找`B`中第一个大于`A[i]`的数的过程，因为`B`是有序的所以用二分查找即可。那么这样下来时间复杂度就是$O(nlogn)$，时间花销远小于上一种办法：

<img src="/Users/liyongyang/Documents/考研/PAT/img/5.png" alt="5" style="zoom:50%;" />



##代码

###$O(n^2)$的代码

```c
#include <stdio.h>
#include <string.h>

int main() {
    int N, M, L, temp;
    scanf("%d", &N);
    int Order[201];
    memset(Order, 0, sizeof(Order));
    scanf("%d", &M);
    for(int i = 1; i <= M; i++){
        scanf("%d", &temp);
        Order[temp] = i;
    }
    
    scanf("%d", &L);
    int A[10001], B[10001], Maxlen = 0, num = 0;
    memset(A, 0, sizeof(A));
    memset(B, 0, sizeof(B));
    
    for(int i = 0; i < L; i++){
        scanf("%d", &temp);
        if(Order[temp] > 0) A[num++] = Order[temp];
    }
    for(int i = 0; i < num; i++){
        B[i] = 1;
        for(int j = 0; j < i; j++){
            if(A[i] >= A[j] && B[j] + 1 > B[i]){
                B[i] = B[j] + 1;
            }
        }
        if(B[i] > Maxlen) Maxlen = B[i];
    }
    printf("%d\n", Maxlen);
    return 0;
}
```

### $O(nlogn)$的代码

```c
#include <stdio.h>
#include <algorithm>
using namespace std;
int Order[201], A[10001], B[10001];
int main() {
    int N, M, L, temp, num = 0;
    scanf("%d %d", &N, &M);
    for(int i = 1; i <= M; i++){
        scanf("%d", &temp);
        Order[temp] = i;
    }
    scanf("%d", &L);
    for(int i = 0; i < L; i++){
        scanf("%d", &temp);
        if(Order[temp] > 0) A[num++] = Order[temp];
    }
    int Idx, len = 0;
    for(int i = 0; i < num; i++){
        Idx = (int)(upper_bound(B, B + len, A[i]) - B);
        B[Idx] = A[i];
        if(Idx == len) len++;
    }
    printf("%d\n", len);
    return 0;
}
```

