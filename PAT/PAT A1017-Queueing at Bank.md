# PAT A1017-Queueing at Bank

## 题目

Suppose a bank has *K* windows open for service. There is a yellow line in front of the windows which devides the waiting area into two parts. All the customers have to wait in line behind the yellow line, until it is his/her turn to be served and there is a window available. It is assumed that no window can be occupied by a single customer for more than 1 hour.

Now given the arriving time *T* and the processing time *P* of each customer, you are supposed to tell the average waiting time of all the customers.

### Input Specification:

Each input file contains one test case. For each case, the first line contains 2 numbers: *N* (≤104) - the total number of customers, and *K* (≤100) - the number of windows. Then *N* lines follow, each contains 2 times: `HH:MM:SS` - the arriving time, and *P* - the processing time in minutes of a customer. Here `HH` is in the range [00, 23], `MM` and `SS` are both in [00, 59]. It is assumed that no two customers arrives at the same time.

Notice that the bank opens from 08:00 to 17:00. Anyone arrives early will have to wait in line till 08:00, and anyone comes too late (at or after 17:00:01) will not be served nor counted into the average.

### Output Specification:

For each test case, print in one line the average waiting time of all the customers, in minutes and accurate up to 1 decimal place.

### Sample Input:

```in
7 3
07:55:00 16
17:00:01 2
07:59:59 15
08:01:00 60
08:00:00 30
08:00:02 2
08:03:00 10
```

### Sample Output:

```out
8.2
```

## 解题思路

首先筛选出来在关门前到达银行的人，然后按时间先后顺序进行排序，之后把那些提前到的人查找出来把他们的到达时间修改成8:00，并且对应地加上等待时间。

之后遍历每一个人，遍历到这个人就是要把这个人分配到某个窗口办理业务，通过对当前顾客的到达时间和窗口上一个服务的客户离开的时间进行对比，可以知道该顾客被分配到该窗口时是否有等待，对应地可以修改增加等待时间的值，每次对窗口添加客人过后都需要计算窗口中占用时间最少的窗口，并且把所有窗口都减去这个最小值，标明按下了快进键直到某个窗口的客人办完业务，窗口不再被占用了。那么一次循环结束了，必然留下了一个可以分配的窗口提供给下一次循环到来的客人。

## 代码

```c
#include <stdio.h>
#include <vector>
#include <algorithm>
#include <string.h>

using namespace std;

typedef struct{
    int arriveTime;
    int P;
} CUSTOMER;

typedef struct{
    int startTime;
    int leaveTime;
    int P;
} WINDOW;

bool cmp(CUSTOMER a, CUSTOMER b){
    return a.arriveTime < b.arriveTime;
}

int FindMinP(WINDOW *windows, int K) {
    int min = 10000000, index = 0;
    for(int i = 0; i < K; i++){
        if(windows[i].P < min){
            min = windows[i].P;
            index = i;
        }
    }
    return index;
}

int main() {
    int N, K;
    scanf("%d %d", &N, &K);
    int HH, MM, SS, P;
    int waitTime = 0, startTime = 8*60*60, closeTime = 17*60*60;
    CUSTOMER temp;
    vector<CUSTOMER> customers;
    for(int i = 0; i < N; i++){
        scanf("%d:%d:%d %d", &HH, &MM, &SS, &P);
        temp.arriveTime = HH*60*60 + MM*60 +SS;
        if(temp.arriveTime < closeTime){
            temp.P = P*60;
            customers.push_back(temp);
        }
    }
    if(customers.size() == 0){
        printf("0.0\n");
        return 0;
    }
    sort(customers.begin(), customers.end(), cmp);
    
    WINDOW windows[K];
    memset(windows, 0, sizeof(windows));
    int minIndex, minusP;
    N = (int)customers.size();
    for(int i = 0; i < N; i++){
        if(customers[i].arriveTime < startTime){
            waitTime += (startTime -customers[i].arriveTime);
            customers[i].arriveTime = startTime;
        }
    }
    for(int i = 0; i < N; i++){
        for(int j = 0; j < K; j++){
            if(windows[j].P == 0){
                if(customers[i].arriveTime < windows[j].leaveTime){// 来的时候还没有窗口
                    windows[j].startTime = windows[j].leaveTime;
                }else {// 来的时候已经有窗口空余了
                    windows[j].startTime = customers[i].arriveTime;
                }
                windows[j].P = customers[i].P;
                windows[j].leaveTime = windows[j].startTime +windows[j].P;
                waitTime = waitTime + (windows[j].startTime - customers[i].arriveTime);
                break;
            }
        }
        minIndex = FindMinP(windows, K);
        minusP = windows[minIndex].P;
        for(int j = 0; j < K; j++){
            windows[j].P -= minusP;
        }
    }
    printf("%.1f\n", waitTime/60.0/N);
    
    return 0;
}
```

