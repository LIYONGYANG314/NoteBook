# PAT A1033-To Fill or Not to Fill

## 题目

With highways available, driving a car from Hangzhou to any other city is easy. But since the tank capacity of a car is limited, we have to find gas stations on the way from time to time. Different gas station may give different price. You are asked to carefully design the cheapest route to go.

### Input Specification:

Each input file contains one test case. For each case, the first line contains 4 positive numbers: *C**m**a**x* (≤ 100), the maximum capacity of the tank; *D* (≤30000), the distance between Hangzhou and the destination city; *D**a**v**g* (≤20), the average distance per unit gas that the car can run; and *N* (≤ 500), the total number of gas stations. Then *N* lines follow, each contains a pair of non-negative numbers: *P**i*, the unit gas price, and *D**i* (≤*D*), the distance between this station and Hangzhou, for *i*=1,⋯,*N*. All the numbers in a line are separated by a space.

### Output Specification:

For each test case, print the cheapest price in a line, accurate up to 2 decimal places. It is assumed that the tank is empty at the beginning. If it is impossible to reach the destination, print `The maximum travel distance = X` where `X` is the maximum possible distance the car can run, accurate up to 2 decimal places.

### Sample Input 1:

```in
50 1300 12 8
6.00 1250
7.00 600
7.00 150
7.10 0
7.20 200
7.50 400
7.30 1000
6.85 300
```

### Sample Output 1:

```out
749.17
```

### Sample Input 2:

```
50 1300 12 2
7.10 0
7.00 600
```

### Sample Output 2:

```
The maximum travel distance = 1200.00
```

## 解题思路

最早的想法是(==斜体部分的想法是错的==)：*当前的状态就是正在某个站点，要去寻找下一个站点，油箱大小是限定了的，所以从一个站点到另一处是有范围限制的，现在要做的就是在这个范围里看看能不能找到一个站点的价格比当前站点的价格还小，如果有那么我就去这个站点加油，如果没有那就在范围里找一个花钱最少的站点就可以了。而每次在站点加油都秉持够用就行的态度，也就是只要刚刚足够到下一个站点就好不要加多了*。

**这样的思路可以保证总在价格最低的地方加油，但是得不到正确答案，因为这样做没有保证每一段路都能尽可能小的开销。比如当前就在一个价格很低的站点，而可到范围内，比如是60米，没有站点的价格比这里还低，经过范围里的价格比较我选出了一个距这里20米处的加油站作为下一个站点加油，那么我照之前的做法只加20米的油足够到达选定站点即可，这样的话在到达选定站点后距离前一个加油站处的范围距离边界还有40米，这四十米就只能用更贵的油来跑。很明显如果我先前在价格便宜的时候我把油装满那么在之后这整个范围内我都可以以更便宜的价格跑完，所以主要的思路就是比较当前站点处的可达范围内有没有比这里价格更低的站点，如果有，那么就只加足够到下一个站点的油就可以了，如果没有，那么把油加满，保证在该站点处划出的可达范围内都是以这么便宜的价格去跑完的。**

## 代码

```c
#include <stdio.h>
#include <algorithm>
using namespace std;
typedef struct{
    double Dist, Price;
} GASSTATION;
bool cmp(GASSTATION a, GASSTATION b){
    return a.Dist < b.Dist;
}
int main(){
    double Cmax, D, Avg;
    int N;
    scanf("%lf %lf %lf %d", &Cmax, &D, &Avg, &N);
    GASSTATION GT[N+1];
    for(int i = 0; i < N; i++){
        scanf("%lf %lf", &GT[i].Price, &GT[i].Dist);
    }
    GT[N].Price = 0;
    GT[N].Dist = D;
    sort(GT, GT+N, cmp);
    if(GT[0].Dist != 0){
        printf("The maximum travel distance = 0.00\n");
        return 0;
    }
    double LeftGasCanGo = 0.0, FromGTCanGo = Cmax * Avg, Min, Total = 0.0;
    int Next = 0;
    for(int i = 0; i < N;){
        Min = 10000;
        Next = -1;
        for(int j = i + 1; j <= N && GT[j].Dist - GT[i].Dist <= FromGTCanGo; j++){// 探寻从该站点能达到的最大距离内的站点
            if(GT[i].Price >= GT[j].Price){
                Next = j;
                Total += (((GT[Next].Dist - GT[i].Dist) / Avg - LeftGasCanGo) * GT[i].Price);
                LeftGasCanGo = 0;
                break;
            }else{
                if(GT[j].Price < Min){
                    Min = GT[j].Price;
                    Next = j;
                }
            }
        }
        if(Next == -1){
            printf("The maximum travel distance = %.2lf\n", FromGTCanGo + GT[i].Dist);
            return 0;
        }
        if(GT[i].Price < GT[Next].Price){
            Total += ((Cmax - LeftGasCanGo) * GT[i].Price);
            LeftGasCanGo = Cmax - (GT[Next].Dist - GT[i].Dist) / Avg;// 到下一个站点还会剩余的油量
        }
        i = Next;
    }
    printf("%.2lf\n", Total);
    return 0;
}

```

