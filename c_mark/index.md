# C 笔记


## 指针

### 有效的指针运算

1. 相同类型指针之间的赋值运算
2. 指针同整数之间的加减运算
3. 指向相同数组中元素的两个指针间的减法或比较运算（相减得到二者之间的元素个数，==、!=、<、>= 等可以针对二者在数组中的位置进行比较）
4. 将指针赋值为 0 或指针和 0 之间的比较运算

不经过强制类型转换而直接把指向一种类型对象的指针赋值给指向另一种类型对象的指针是不合法的，除非其中之一是 void * 类型的指针。

### 指针数组与多维数组

> int a[10][20];
> int *b[10]; // 10 个指针元素

如果同样存储 10X20 的的元素，二维数组 a 分配 200 个 int 类型长度的存储空间，而指针数组 b 还要额外分配 10 个指针的存储空间。

指针数组的优势在于每个指针元素可以指向不同长度，比较频繁用于存放具有不同长度的字符串。

> char *month[] = {"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"};

### 复杂声明

> int *f()  
> int (*pf)()  

f 是函数，返回值是指向 int 类型的指针  
pf 是指针，指向一个返回值是 int 的函数  
\* 是一个前缀运算符，其优先级低于函数调用 ()

> char **argv  
> int (*pa)[13]  
> int *a[13]  

argv 是指针，指向 char \*  
pa 是指针，指向 array[13] of int（保存 13 个 int 元素的数组）  
a 是数组，保存了 13 个指向 int 类型的指针

### 函数指针

C 的函数指针很强大，但是并不具备闭包的功能。

``` c
struct node {
    int val;
    struct node *next;
}

typedef void (*print_node)(struct node *, int);

void func(struct node *t, int i) {
    printf("%d: %d\n", i++, t->val);
}

// void foreach(struct node *t, print_node f)
void foreach(struct node *t, void (*f)(struct node *, int)) {
    int i = 0;
    while (t) {
        f(t, i);
        t = t->next;
    }
}

int main(void) {
    print_node f;
    f = func;

    struct node *root = NULL, *t = NULL;
    int i;
    for (i = 0; i < 10; i++) {
        t = (struct node *)malloc(sizeof(struct node));
        t->val = i;
        t->next = root;
        root = t;
    }
    foreach(root, func);
    return 0;
}
```

## 位运算

x^y = (x&~y) | (~x&y)

