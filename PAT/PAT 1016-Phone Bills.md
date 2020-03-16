# PAT 1016-Phone Bills

## 题目

A long-distance telephone company charges its customers by the following rules:

Making a long-distance call costs a certain amount per minute, depending on the time of day when the call is made. When a customer starts connecting a long-distance call, the time will be recorded, and so will be the time when the customer hangs up the phone. Every calendar month, a bill is sent to the customer for each minute called (at a rate determined by the time of day). Your job is to prepare the bills for each month, given a set of phone call records.

### Input Specification:

Each input file contains one test case. Each case has two parts: the rate structure, and the phone call records.

The rate structure consists of a line with 24 non-negative integers denoting the toll (cents/minute) from 00:00 - 01:00, the toll from 01:00 - 02:00, and so on for each hour in the day.

The next line contains a positive number *N* (≤1000), followed by *N* lines of records. Each phone call record consists of the name of the customer (string of up to 20 characters without space), the time and date (`mm:dd:hh:mm`), and the word `on-line` or `off-line`.

For each test case, all dates will be within a single month. Each `on-line` record is paired with the chronologically next record for the same customer provided it is an `off-line` record. Any `on-line` records that are not paired with an `off-line` record are ignored, as are `off-line` records not paired with an `on-line` record. It is guaranteed that at least one call is well paired in the input. You may assume that no two records for the same customer have the same time. Times are recorded using a 24-hour clock.

### Output Specification:

For each test case, you must print a phone bill for each customer.

Bills must be printed in alphabetical order of customers' names. For each customer, first print in a line the name of the customer and the month of the bill in the format shown by the sample. Then for each time period of a call, print in one line the beginning and ending time and date (`dd:hh:mm`), the lasting time (in minute) and the charge of the call. The calls must be listed in chronological order. Finally, print the total charge for the month in the format shown by the sample.

### Sample Input:

```in
10 10 10 10 10 10 20 20 20 15 15 15 15 15 15 15 20 30 20 15 15 10 10 10
10
CYLL 01:01:06:01 on-line
CYLL 01:28:16:05 off-line
CYJJ 01:01:07:00 off-line
CYLL 01:01:08:03 off-line
CYJJ 01:01:05:59 on-line
aaa 01:01:01:03 on-line
aaa 01:02:00:01 on-line
CYLL 01:28:15:41 on-line
aaa 01:05:02:24 on-line
aaa 01:04:23:59 off-line
```

### Sample Output:

```out
CYJJ 01
01:05:59 01:07:00 61 $12.10
Total amount: $12.10
CYLL 01
01:06:01 01:08:03 122 $24.40
28:15:41 28:16:05 24 $3.85
Total amount: $28.25
aaa 01
02:00:01 04:23:59 4318 $638.80
Total amount: $638.80
```

## 解题思路

做这道题要解决三个问题：

- 首先是数据需要排序，用户数据要按照名字的字典序还有数据产生的时间进行排序。如果用的是map存储数据那么名字作为键值会被自动排序；如果不是那么sort的比较函数通过strcmp来比较名字大小。
- 有了排序然后是对于费用的计算，我最早用的方法是：首先对于时间要进行处理，最好是把他们换算成到本月1号0点0分之间的时间差，然后先把分钟位上的时间费用计算出来，然后把on和off的时间减去或是补加到邻近整数小时处，之后以on为开始off为结束进行循环，然后达到对应时间通过以24为模求余可得到当前时间在一天中对应的时间之后可以对应的到相应的rate，然后每次累加最终得到花费。然后网上还有两种办法，一是循环中以off时间作为结束点然后从开始时间进行分钟数的累加分钟数每到60则分钟归零小时数加一，小时与天的关系也相应如此变化，变化的同时累加对应的rate值。还有种办法是在柳神那看的，以每月最起始的时间作为一个点，然后计算on和off相对于这个起始时间的话费花销之后做差就得到on-off之间的话费(意思是这个意思但实际算出来的时候是以上个月的最后一天的0点0分为基准，因为这样更方便写一点)。
- 最后是打印的问题，可以用`map`和`vector`这么写思路会很清晰。不用的话会比较绕一点，这版代码用了两个指针`end`和`start`他们标明了一个用户的数据边界，`start`指向一个用户的第一个数据，`end`负责遍历，当前后数据名字不一样的时候end就到了另一个用户的第一条数据了，此时就暂停遍历，进行打印，在遍历过程中会判断该用户是否存在有效数据，如果有留下一个标记，之后依靠这个标记就可以判断是否需要打印该用户的账单，如果需要，那么就由`start->end`又遍历一次，找到有两个数据按照`on-off`的顺序排列那么这两个数据就是有效数据要被打印。当遍历到end前一个后循环停止，打印出`Total`。不断往复知道所有数据都打印出来。

### 特别说一下：

这道题，我写了至少有8个小时才AC，其间一共写了三个版本的代码，因为很倒霉，在没做出来去搜题解时第一篇就遇到了一个老哥的提示，他说：*本题最大的坑：**输出时只输出话费大于0的顾客，**如果话费为0，连名字都不输出*。所以每一版代码对于输出都加了一个判断，就是话费小于0不输出，结果总有一个点过不了。而且这条论断不止在一篇博文里出现过，所以我很久都觉得没问题是我的逻辑错了，之后我把用他的代码运行了一下有话费为0的数据，得到下面的结果：

//放结果

//问号

现在AC了之前的思路都是没有问题的，出问题的点在于跨天时间的计算以及算总和时候Total对每位用户置0时的地方有问题。

所以如果有同学也看到了这样的结论，现在为各位纠正了。反正我的8小时就这么白白花了。

## 代码

```c
#include <stdio.h>
#include <string.h>
#include <algorithm>
using namespace std;
typedef struct{
    char Name[25];
    int MM, DD, H, M, time;
    bool IsOnLine;
} RECORD;
bool cmp(RECORD a, RECORD b){
    int s = strcmp(a.Name, b.Name);
    if(s == 0) return a.time < b.time;
    return s < 0;
}
double ComputeSum(int *Rate, RECORD R){
    double total = Rate[R.H] * R.M + Rate[24] * 60 * R.DD;
    for(int i = 0; i < R.H; i++){
        total += (Rate[i] * 60);
    }
    return (total / 100.0);
}
int main() {
    int Rate[25] = {0}, N;
    for(int i = 0; i < 24; i++){
        scanf("%d", &Rate[i]);
        Rate[24] += Rate[i];
    }
    scanf("%d", &N);
    RECORD Rcd[N];
    char status[10];
    for(int i = 0; i < N; i++){
        scanf("%s", Rcd[i].Name);
        scanf("%d:%d:%d:%d", &Rcd[i].MM, &Rcd[i].DD, &Rcd[i].H, &Rcd[i].M);
        scanf("%s", status);
        if(strcmp(status, "on-line") == 0) Rcd[i].IsOnLine = true;
        else Rcd[i].IsOnLine = false;
        Rcd[i].time = Rcd[i].DD*24*60 + Rcd[i].H*60 + Rcd[i].M;
    }
    sort(Rcd, Rcd+N, cmp);
    int start = 0, end = 0;
    bool IsPrint;
    double Sum, Total;
    while(start < N){
        IsPrint = false;
        start = end;
        Total = 0.0;
        for(end = start+1; end < N; end++){//前后的名字不同，说明end已经到了新的一段用户数据了
            if(strcmp(Rcd[end].Name, Rcd[end-1].Name) != 0) break;
            if(!Rcd[end].IsOnLine && Rcd[end-1].IsOnLine) IsPrint = true;
        }
        if(IsPrint){// 说明可以打印有效数据
            printf("%s %02d\n",Rcd[start].Name, Rcd[start].MM);
            for(int i = start+1; i < end; i++){//这里是找end之前的有效数据，所以end-1与其后以为根本不可能构成有效数据，那么检查到end-1之前
                if(Rcd[i-1].IsOnLine && !Rcd[i].IsOnLine){// 有效数据
                    Sum = ComputeSum(Rate, Rcd[i]) - ComputeSum(Rate, Rcd[i-1]);
                    printf("%02d:%02d:%02d %02d:%02d:%02d %d $%.2f\n", Rcd[i-1].DD, Rcd[i-1].H, Rcd[i-1].M, Rcd[i].DD, Rcd[i].H, Rcd[i].M, Rcd[i].time-Rcd[i-1].time, Sum);
                    Total += Sum;
                }
            }
            printf("Total amount: $%.2f\n", Total);
        }
    }
    return 0;
}

```



