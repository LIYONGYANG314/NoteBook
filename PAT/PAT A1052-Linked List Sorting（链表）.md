# PAT A1052-Linked List Sorting（链表）

## 题目

A linked list consists of a series of structures, which are not necessarily adjacent in memory. We assume that each structure contains an integer `key` and a `Next` pointer to the next structure. Now given a linked list, you are supposed to sort the structures according to their key values in increasing order.

### Input Specification:

Each input file contains one test case. For each case, the first line contains a positive *N* (<105) and an address of the head node, where *N* is the total number of nodes in memory and the address of a node is a 5-digit positive integer. NULL is represented by −1.

Then *N* lines follow, each describes a node in the format:

```
Address Key Next
```

where `Address` is the address of the node in memory, `Key` is an integer in [−105,105], and `Next` is the address of the next node. It is guaranteed that all the keys are distinct and there is no cycle in the linked list starting from the head node.

### Output Specification:

For each test case, the output format is the same as that of the input, where *N* is the total number of nodes in the list and all the nodes must be sorted order.

### Sample Input:

```in
5 00001
11111 100 -1
00001 0 22222
33333 100000 11111
12345 -1 33333
22222 1000 12345
```

### Sample Output:

```out
5 12345
12345 -1 00001
00001 0 11111
11111 100 22222
22222 1000 33333
33333 100000 -1
```

## 解题思路

一开始我的理解是直接把所给的所有节点按照数据值的大小进行排序形成新链表就可以，但是实际上如果按照这种理解那么next和head以及输出的N将毫无意义，所以转变思路，题目应该是要求将头指针指向的链表按排序后输出，那么只需要读取完所有数据后，遍历各个节点直到遇到-1时，这就代表头指针指向的链表结束了。得到了头指针指向的完整链表后，对链表按照数据值的大小进行排序，最后调整next的值即可。

## 代码

```c
#include <stdio.h>
#include <algorithm>
#define MAXN 100010

using namespace std;

typedef struct {
    int addr;
    int Data;
    int Next;
} NODE;

bool cmp(NODE a, NODE b){
    return a.Data < b.Data;
}

int main() {
    int N, head;
    int addr;
    NODE allnode[MAXN];// hash存储节点
    scanf("%d %d", &N, &head);
    NODE node[N];// 用于存储头指针指向链表的所有节点
    for(int i = 0; i < N; i++){
        scanf("%d ", &addr);
        scanf("%d %d", &allnode[addr].Data, &allnode[addr].Next);
        allnode[addr].addr = addr;
    }
  	// 遍历链表得到完整的头指针指向的节点然后把它存储在node里
    for(N = 0; head != -1; N++){
        node[N] = allnode[head];
        head = allnode[head].Next;
    }
    sort(node, node+N, cmp);// 排序
    printf("%d ", N);
    for(int i = 0; i < N; i++){
        printf("%05d\n", node[i].addr);
        printf("%05d %d ", node[i].addr, node[i].Data);
    }
    printf("%d\n", -1);
    
    return 0;
}

```

