# PAT A1032-Sharing（链表）

## 题目

To store English words, one method is to use linked lists and store a word letter by letter. To save some space, we may let the words share the same sublist if they share the same suffix. For example, `loading` and `being` are stored as showed in Figure 1.

![fig.jpg](https://images.ptausercontent.com/ef0a1fdf-3d9f-46dc-9a27-21f989270fd4.jpg)

Figure 1

You are supposed to find the starting position of the common suffix (e.g. the position of `i` in Figure 1).

### Input Specification:

Each input file contains one test case. For each case, the first line contains two addresses of nodes and a positive *N* (≤105), where the two addresses are the addresses of the first nodes of the two words, and *N* is the total number of nodes. The address of a node is a 5-digit positive integer, and NULL is represented by −1.

Then *N* lines follow, each describes a node in the format:

```
Address Data Next
```

where`Address` is the position of the node, `Data` is the letter contained by this node which is an English letter chosen from { a-z, A-Z }, and `Next` is the position of the next node.

### Output Specification:

For each case, simply output the 5-digit starting position of the common suffix. If the two words have no common suffix, output `-1` instead.

### Sample Input 1:

```in
11111 22222 9
67890 i 00002
00010 a 12345
00003 g -1
12345 D 67890
00002 n 00003
22222 B 23456
11111 L 00001
23456 e 67890
00001 o 00010
```

### Sample Output 1:

```out
67890
```

### Sample Input 2:

```in
00001 00002 4
00001 a 10001
10001 s -1
00002 a 10002
10002 t -1
```

### Sample Output 2:

```out
-1
```

## 解题思路

把所有节点先存储，在遍历第一个单词的链表时，在总的节点链表里将遍历到的点标记出来，之后再遍历第二个单词的链表，当遍历到有第一单词标记的点时，说明该点就是我们要找的点把地址打出来即可。

## 代码

```c
#include <stdio.h>

using namespace std;

// 每个节点的结构
typedef struct {
    bool flag;
    char Data;
    int Next;
} NODE;

int main() {
    int fir, sec, N, addr;
    NODE table[100010];// 题目说明最多不会超过100000条数据
    scanf("%d %d %d", &fir, &sec, &N);
    
    for(int i = 0; i < N; i++){
        scanf("%d ", &addr);// 方便快速查找
        scanf("%c %d", &table[addr].Data, &table[addr].Next);
//        Table[addr].flag = false;
    }
    while(fir != -1){
        table[fir].flag = true;// 第一个单词遍历过的节点flag都标记为true
        fir = table[fir].Next;
    }
    while(sec != -1){
        if(table[sec].flag == true){// 说明第一个单词也遍历过这个节点，直接输出地址
            printf("%05d\n", sec);// 格式要求是输出五位
            return 0;
        }
        sec = table[sec].Next;
    }
    printf("%d\n", -1);
    return 0;
}

```

之前用了<map>来实现，但是时间花销比较大，因为map得到的hash表是有序的，这部分的排序会花销时间，所以如果没有对排序的需求，并且hash表范围能用数组表示的时候最好还是用数组吧。