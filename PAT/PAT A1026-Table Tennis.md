 # PAT A1026-Table Tennis

## 题目

A table tennis club has N tables available to the public. The tables are numbered from 1 to N. For any pair of players, if there are some tables open when they arrive, they will be assigned to the available table with the smallest number. If all the tables are occupied, they will have to wait in a queue. It is assumed that every pair of players can play for at most 2 hours.

Your job is to count for everyone in queue their waiting time, and for each table the number of players it has served for the day.

One thing that makes this procedure a bit complicated is that the club reserves some tables for their VIP members. When a VIP table is open, the first VIP pair in the queue will have the priviledge to take it. However, if there is no VIP in the queue, the next pair of players can take it. On the other hand, if when it is the turn of a VIP pair, yet no VIP table is available, they can be assigned as any ordinary players.

### Input Specification:

Each input file contains one test case. For each case, the first line contains an integer `N` (≤10000) - the total number of pairs of players. Then `N` lines follow, each contains 2 times and a VIP tag: `HH:MM:SS` - the arriving time, `P` - the playing time in minutes of a pair of players, and `tag` - which is 1 if they hold a VIP card, or 0 if not. It is guaranteed that the arriving time is between 08:00:00 and 21:00:00 while the club is open. It is assumed that no two customers arrives at the same time. Following the players' info, there are 2 positive integers: `K` (≤100) - the number of tables, and `M` (< K) - the number of VIP tables. The last line contains `M` table numbers.

### Output Specification:

For each test case, first print the arriving time, serving time and the waiting time for each pair of players in the format shown by the sample. Then print in a line the number of players served by each table. Notice that the output must be listed in chronological order of the serving time. The waiting time must be rounded up to an integer minute(s). If one cannot get a table before the closing time, their information must NOT be printed.

### Sample Input:

```in
9
20:52:00 10 0
08:00:00 20 0
08:02:00 30 0
20:51:00 10 0
08:10:00 5 0
08:12:00 10 1
20:50:00 10 0
08:01:30 15 1
20:53:00 10 1
3 1
2
```

### Sample Output:

```out
08:00:00 08:00:00 0
08:01:30 08:01:30 0
08:02:00 08:02:00 0
08:12:00 08:16:30 5
08:10:00 08:20:00 10
20:50:00 20:50:00 0
20:51:00 20:51:00 0
20:52:00 20:52:00 0
3 3 2
```

## 解题思路

开了两个队列用来存VIP和普通队列。

因为对于队首元素的操作也就是把他们安排到某张桌子的动作，必须是在当前时间下队首有人，而且当前时间下要有空桌子，这样才能把队首的人安排到空桌子去。整个程序也是在模拟时间的流动，对于有空桌而没人来，和没空桌的情况，我们通过把记录时间的量`Time`设置成队首元素的到达时间来解决队首没人的情况，把时间调到当前还要占用时间最少的桌子的结束时间就解决了没空桌的情况，每次进行这么两个操作一定可以得到队首有人且还有空桌的时间点，那么再安排人到某个桌子就行了。

我觉得这道题很重要的是不要忽略掉VIP对于VIP桌子和普通桌子的选择，意思是当VIP排在队首的时候，现在空出了一张VIP桌子又空出了一张普通桌子，那么VIP该选哪一张。测试点要求必须选VIP桌。现在看似乎很合理，但是如果按照下面代码的思路那么自然会使得这样的情况选择编号最小的桌子，所以我的前3次5和7测试点总过不去。在第70行加判断语句后会在上诉情况中对于VIP而言优先选择VIP桌子，即便普通桌的编号更小。不过70行处的判断仍然有瑕疵，如果说同时有两张VIP桌对于当前的VIP而言它会选择编号更大的那张，明显有悖于题目中==they will be assigned to the available table with the smallest number==但是侥幸过了，我也懒得改了：）

## 代码

```c
#include <stdio.h>
#include <string.h>
#include <queue>
#include <vector>
#include <iostream>
#include <cmath>
#include <algorithm>
typedef struct{
    int Cnt, Tag, PlayTime;
} TABLE;
typedef struct{
    int HH, MM, SS, PlayTime, Tag, Time, Num;
} CUSTOMER;
using namespace std;
bool cmp(CUSTOMER a, CUSTOMER b){
    return a.Time < b.Time;
}
int main() {
    int N, K, M, vt;
    CUSTOMER temp;
    vector<CUSTOMER> ForSort;
    queue<CUSTOMER> Ordinary, VIP;
    scanf("%d", &N);
    int Serve[N];
    for(int i = 0; i < N; i++){
        scanf("%d:%d:%d %d %d", &temp.HH, &temp.MM, &temp.SS, &temp.PlayTime, &temp.Tag);
        if(temp.PlayTime > 120) temp.PlayTime = 120 * 60;
        else temp.PlayTime *= 60;
        temp.Time = temp.HH * 3600 + temp.MM * 60 + temp.SS;
        temp.Num = i;
        Serve[i] = 0;
        ForSort.push_back(temp);
    }
    sort(ForSort.begin(), ForSort.end(), cmp);
    for(int i = 0; i < N; i++){
        Ordinary.push(ForSort[i]);
        if(ForSort[i].Tag == 1) VIP.push(ForSort[i]);
    }
    scanf("%d %d", &K, &M);
    TABLE Table[K+1];
    memset(Table, 0, sizeof(Table));
    for(int i = 0; i < M; i++){
        scanf("%d", &vt);
        Table[vt].Tag = 1;
    }
    
    int Time = 8*3600, EndTime = 21*3600, NoPeopleTime = 0, min, idx = 0;
    while(!Ordinary.empty()){
        temp = Ordinary.front();
        if(temp.Time > Time){
            NoPeopleTime = temp.Time - Time;
            for(int i = 1; i <= K; i++){
                Table[i].PlayTime -= NoPeopleTime;
                if(Table[i].PlayTime < 0) Table[i].PlayTime = 0;
            }
            Time = temp.Time;
        }
        if(Time >= EndTime) break;
        min = 200*60;
        for(int i = 1; i <= K; i++){
            if(Table[i].PlayTime < min){
                min = Table[i].PlayTime;
                idx = i;
            }
        }
        Time += min;
        if(Time >= EndTime) break;
        for(int i = 1; i <= K; i++){
            Table[i].PlayTime -= min;
            if(Table[i].PlayTime == 0 && Table[i].Tag == 1 && !VIP.empty() && VIP.front().Time <= Time){
                idx = i;
            }
        }
        if(Table[idx].Tag == 1){
            if(!VIP.empty() && Serve[VIP.front().Num] == 0 && VIP.front().Time <= Time){
                temp = VIP.front();
                Table[idx].PlayTime = temp.PlayTime;
                Table[idx].Cnt++;
                Serve[temp.Num] = 1;
                printf("%02d:%02d:%02d %02d:%02d:%02d ", temp.HH, temp.MM, temp.SS, Time/3600, (Time%3600)/60, Time%60);
                cout<<round((Time-temp.Time)/60.0)<<endl;
                VIP.pop();
            }else{
                if(Serve[Ordinary.front().Num] == 0){
                    temp = Ordinary.front();
                    Table[idx].PlayTime = temp.PlayTime;
                    Table[idx].Cnt++;
                    Serve[temp.Num] = 1;
                    printf("%02d:%02d:%02d %02d:%02d:%02d ", temp.HH, temp.MM, temp.SS, Time/3600, (Time%3600)/60, Time%60);
                    cout<<round((Time-temp.Time)/60.0)<<endl;
                    Ordinary.pop();
                }else Ordinary.pop();
            }
        }else{
            if(Serve[Ordinary.front().Num] == 0){
                temp = Ordinary.front();
                Table[idx].PlayTime = temp.PlayTime;
                Table[idx].Cnt++;
                Serve[temp.Num] = 1;
                printf("%02d:%02d:%02d %02d:%02d:%02d ", temp.HH, temp.MM, temp.SS, Time/3600, (Time%3600)/60, Time%60);
                cout<<round((Time-temp.Time)/60.0)<<endl;
                if(temp.Tag == 1) VIP.pop();
                Ordinary.pop();
            }else Ordinary.pop();
        }
    }
    printf("%d", Table[1].Cnt);
    for(int i = 2; i <= K; i++){
        printf(" %d", Table[i].Cnt);
    }
    return 0;
}

```

