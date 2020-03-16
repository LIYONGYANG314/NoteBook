# STL

## vector

头文件`<vector>`

迭代器遍历：`for(vector<type>::iterator it = v.begin(); it != v.end(); it++)`迭代器的取数直接用`*it`就行

访问元素`v[i]`	添加元素`v.push_back(n)`	删除尾末元素`v.pop_back`	获取长度`v.size()`	清空所有元素`v.clear()`	删除迭代器处元素`v.erase(it)`	运用`sort`排序`sort(v.begin(), v.end(), cmp)`

## queue

头文件`<queue>`

`front()`获得队首元素	`back()`获得队尾元素

`push()`入队	`pop()`出队

`empty()`队列是否为空，返回值为`true`或`false`

`size()`队列元素个数

## map

可以调用`map()`函数去做。头文件是`#include <map>`

`map<char, int>::iterator it`迭代器用`mp.find('key')`赋值，`erase`可以单个或是多个元素删除，以`it`来删，`clear()`清除所有的元素，`size`获取映射的大小。**map**会依据键值由小到大排序，`mp.find()`如果有该元素，那么会返回迭代器，如果没有的话那么就返回`mp.end()`，使用`mp.count()`，返回的是被查找元素的个数。如果有，返回1；否则，返回0。`it&mp`都是两个元素`first`(key值)和`second`(值)。`unordered_map`是没有排序需要的map，速度更快。

## sort

头文件`<algorithm>`

`sort(首地址, 尾地址的下一位, cmp)`关于`cmp`：

```c
// 由大到小排列
bool cmp(type a, type b){
  return a > b
}
```

上面这么记：输入参数表示按照`a b`的顺序存储可以吗？返回`false`表示不可以，那么顺序就会是`b a`，返回`true`表示可以，顺序就是`a b`

