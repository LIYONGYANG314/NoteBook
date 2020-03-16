# AVL生成

```c
#include <stdio.h>
#include <algorithm>
#include <string.h>
using namespace std;

struct node{
    int w, height;
    node *lchild, *rchild;
};
typedef struct node NODE;
//生成新节点，权值为w
NODE* newNode(int w){
    NODE *node = new NODE;
    node->w = w;
    node->height = 1;
    node->lchild = NULL;
    node->rchild = NULL;
    return node;
}
//以root为根节点的子树当前的高度
int getHeight(NODE *root){
    if(root == NULL) return 0;
    return root->height;
}
//定义：平衡因子是左子树高度-右子树高度。根据定义求平衡因子
int getBalanceFactor(NODE *root){
    return getHeight(root->lchild) - getHeight(root->rchild);
}
//更新root的height，孩子里的最大高度，加上自己作为根节点的这一层。
void updateHeight(NODE *root){
    root->height = max(getHeight(root->lchild), getHeight(root->rchild)) + 1;
}
//左旋对于成链的树来说是很友好的，直接逆时针转转一下就行了，但是，
//对于子树上左右子树都存在的情况，需要把孩子移到另外的节点上面去，
//但是对于以上的两种情况下面的转换都是可行的(图解见P322)
void L(NODE* &root){
    NODE *temp = root->rchild;
    root->rchild = temp->lchild;
    temp->lchild = root;
    updateHeight(root);
    updateHeight(temp);
    root = temp;
}
//同理右旋
void R(NODE* &root){
    NODE *temp = root->lchild;
    root->lchild = temp->rchild;
    temp->rchild = root;
    updateHeight(root);
    updateHeight(temp);
    root = temp;
}
void insert(NODE* &root, int w){
    if(root == NULL){
        root = newNode(w);
        return;
    }
    if(w < root->w){
        insert(root->lchild, w);
        updateHeight(root);
        if(getBalanceFactor(root) == 2){
            if(getBalanceFactor(root->lchild) == 1){ // LL型
                R(root);
            }else if(getBalanceFactor(root->lchild) == -1){
                L(root->lchild);
                R(root);
            }
        }
    }else {
        insert(root->rchild, w);
        updateHeight(root);
        if(getBalanceFactor(root) == -2){
            if(getBalanceFactor(root->rchild) == -1){
                L(root);
            }else if(getBalanceFactor(root->rchild) == 1){
                R(root->rchild);
                L(root);
            }
        }
    }
}
int main() {
    int N, temp;
    scanf("%d", &N);
    NODE *root = NULL;
    for(int i = 0; i < N; i++){
        scanf("%d", &temp);
        insert(root, temp);
    }
    printf("%d\n", root->w);
    return 0;
}

```

