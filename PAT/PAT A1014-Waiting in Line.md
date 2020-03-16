# PAT A1014-Waiting in Line

## 题目

Suppose a bank has *N* windows open for service. There is a yellow line in front of the windows which devides the waiting area into two parts. The rules for the customers to wait in line are:

- The space inside the yellow line in front of each window is enough to contain a line with *M* customers. Hence when all the *N* lines are full, all the customers after (and including) the (*N**M*+1)st one will have to wait in a line behind the yellow line.
- Each customer will choose the shortest line to wait in when crossing the yellow line. If there are two or more lines with the same length, the customer will always choose the window with the smallest number.
- *C**u**s**t**o**m**e**r**i* will take *T**i* minutes to have his/her transaction processed.
- The first *N* customers are assumed to be served at 8:00am.

Now given the processing time of each customer, you are supposed to tell the exact time at which a customer has his/her business done.

For example, suppose that a bank has 2 windows and each window may have 2 custmers waiting inside the yellow line. There are 5 customers waiting with transactions taking 1, 2, 6, 4 and 3 minutes, respectively. At 08:00 in the morning, *c**u**s**t**o**m**e**r*1 is served at *w**i**n**d**o**w*1 while *c**u**s**t**o**m**e**r*2 is served at *w**i**n**d**o**w*2. *C**u**s**t**o**m**e**r*3 will wait in front of *w**i**n**d**o**w*1 and *c**u**s**t**o**m**e**r*4 will wait in front of *w**i**n**d**o**w*2. *C**u**s**t**o**m**e**r*5 will wait behind the yellow line.

At 08:01, *c**u**s**t**o**m**e**r*1 is done and *c**u**s**t**o**m**e**r*5 enters the line in front of *w**i**n**d**o**w*1 since that line seems shorter now. *C**u**s**t**o**m**e**r*2 will leave at 08:02, *c**u**s**t**o**m**e**r*4 at 08:06, *c**u**s**t**o**m**e**r*3 at 08:07, and finally *c**u**s**t**o**m**e**r*5 at 08:10.

### Input Specification:

Each input file contains one test case. Each case starts with a line containing 4 positive integers: *N* (≤20, number of windows), *M* (≤10, the maximum capacity of each line inside the yellow line), *K* (≤1000, number of customers), and *Q* (≤1000, number of customer queries).

The next line contains *K* positive integers, which are the processing time of the *K* customers.

The last line contains *Q* positive integers, which represent the customers who are asking about the time they can have their transactions done. The customers are numbered from 1 to *K*.

### Output Specification:

For each of the *Q* customers, print in one line the time at which his/her transaction is finished, in the format `HH:MM` where `HH` is in [08, 17] and `MM` is in [00, 59]. Note that since the bank is closed everyday after 17:00, for those customers who cannot be served before 17:00, you must output `Sorry` instead.

### Sample Input:

```in
2 2 7 5
1 2 6 4 3 534 2
3 4 5 6 7
```

### Sample Output:

```out
08:07
08:06
08:10
17:00
Sorry
```

## 解题思路

`N`个窗口对应N个队列，队列的长度是有限制的，而黄线内的人员排队是选择最短的队列进行排队，如果队长同等那么选择窗口标号更小的队列排队。在人数充足的情况下：1、首先应该先填满窗口，让每个窗口都有人，并且在填窗口时代表有人正在窗口接受服务，所以此时要计算开始时间离开时间，2、然后填满黄线前的N个队列，3、最后不断把顾客往黄线里面填直到出现黄线前的队列是满的，所有顾客都已经进到黄线区域中即可，这个过程也涉及到窗口会服务队首的人所以也要相应地计算顾客在窗口得到服务的开始时间和结束时间。这三个过程用三个循环可以完成，必须限定循环进行的条件之一是`i`小于总人数。经过这个过程最终得到N个队列，N个队列互不关联，进行N次循环，把队列所有元素取出计算得到服务结束时间，最后把得到的结果打印出来即可。

题目读懂了就完成一大半了，题目理解错了，那就白瞎几个小时去找错。之前把17:00关门那个信息理解成了17:00之后就不再对客户提供服务，然后错误地写出判断条件是客户服务结束时间在17:00后的打印`sorry`，结果找半天错，很难受。完了现在仔细一读，其实姥姥说得也挺明确了==for those customers who cannot be served before 17:00, you must output `Sorry` instead.==——在17:00没能被服务的客户需要输出`sorry`。就是服务开始时间在17:00及以后的客户就不能被服务了，但是17:00之前就开始服务的客户依然是能被服务的。

仔细读题很重要。

## 代码

```c
#include <stdio.h>
#include <queue>

using namespace std;
typedef struct{
    int StartTime;
    int ProcessedTime;
    int LeaveTime;
} WINDOWS;
typedef struct{
    int num;
    int P;
} CUSTOMER;

int main() {
    int Open = 8 * 60, Close = 17 * 60;
    int N, M, K, Q;
    scanf("%d %d %d %d", &N, &M, &K, &Q);
    WINDOWS windows[N];
    queue<CUSTOMER> InsideLine[N];// 每个窗口前面小于M人
    CUSTOMER customer[K];
    for(int i = 0; i < K; i++){
        scanf("%d", &customer[i].P);
        customer[i].num = i + 1;
    }
    int P[Q+1];
    for(int i = 0; i < Q; i++){
        scanf("%d", &P[i]);
    }
   
    int min, index, res[2000];
    for(int i = 0; i < N && i < K; i++){
        windows[i].LeaveTime = Open + customer[i].P;
        windows[i].ProcessedTime = customer[i].P;
        windows[i].StartTime = Open;
        InsideLine[i].push(customer[i]);
        res[customer[i].num] = windows[i].LeaveTime;
    }
    for(int i = N; i < (N * M) && i < K; i++){
        InsideLine[i%N].push(customer[i]);
    }
    for(int i = M * N; i < K; i++){
        
        min = 100000;
        index = 0;
        for(int j = 0; j < N; j++){
            if(windows[j].ProcessedTime < min){
                min = windows[j].ProcessedTime;
                index = j;
            }
        }
        for(int j = 0; j < N; j++){
            windows[j].ProcessedTime -= min;
        }
        InsideLine[index].pop();
        windows[index].ProcessedTime = InsideLine[index].front().P;
        windows[index].StartTime = windows[index].LeaveTime;
        windows[index].LeaveTime = windows[index].StartTime + windows[index].ProcessedTime;
        res[InsideLine[index].front().num] = windows[index].LeaveTime;
        InsideLine[index].push(customer[i]);
    }
    for(int j = 0; j < N; j++){
        if(InsideLine[j].empty()) continue;
        InsideLine[j].pop();
        while(!InsideLine[j].empty()){
            windows[j].ProcessedTime = InsideLine[j].front().P;
            windows[j].StartTime = windows[j].LeaveTime;
            windows[j].LeaveTime = windows[j].StartTime + windows[j].ProcessedTime;
            res[InsideLine[j].front().num] = windows[j].LeaveTime;
            InsideLine[j].pop();
        }
    }
    for(int i = 0; i < Q; i++){
        if(res[P[i]] - customer[P[i]-1].P < Close){
            printf("%02d:%02d\n", res[P[i]]/60, res[P[i]]%60);
        }else{
            printf("Sorry\n");
        }
    }
    return 0;
}
```

