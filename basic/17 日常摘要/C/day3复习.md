## 复习

1、C 程序架构

```c
#include <stdio.h>
int main() {
  printf("我是第一学渣");
  return 0;
}
```

2、类型（int/float/double/char）

```c
int a = 3;
float a = 3.14;
double a = 3.14;
char a = 'a';
```

3、使用printf语句输出(int/float/double/char)

```c
#include <stdlib.h>
int main() {
    double a,b,c;
    a = 3.25;
    b = 1.13;
    c = 'hello';
    printf("a=%f\nb=%f\n",a,b);
    printf("c=%c\n",c);
    return 0;
}
```

4、使用scanf语句输出（int/float/double/char）

(1)输出int类型

```c
#include <stdlib.h>
int main() {
    int a,b,c;
    printf("请输出：\n");
    scanf("%d,%d",&a,&b);
    c = (a + b) /2;
    printf("%d与%d的平均数为%d\n",a,b,c);
    return 0;
}
```

(2)输出char类型

栗子：请编程序，输入一个大写字母，可以输出一个小写字母

```c
#include <stdio.h>
int main(){
  char a,b;
  printf("请输出大写字母：\n");
  scanf("%c",&a);
  b = a + 32;
  printf("%c的小写字母是%c",a,b);
  return 0;
}
```

5、putchar()语句和getchars()语句

```c
#include <stdio.h>
int main(){
    char a,b,c;
    a=getchar();
    b=getchar();
    c=getchar();
    a = a + 32;
    b = b + 32;
    c = c + 32;
    putchar(a);
    putchar(b);
    putchar(c);
    putchar('\n');
    return 0;
}
```

6、数学运算

```c
#include <stdio.h>
#include <math.h>
int main(){
    double a,b,c,s,area;
    printf("请输出：\n");
    scanf("%lf,%lf,%lf",&a,&b,&c);
    s = (a + b + c) / 2;
    area = sqrt(s*(s-a)*(s-b)*(s-c));
    printf("a=%f\tb=&f\tc=%f\n",a,b,c);
    printf("area=%f\n",area);
    return 0;
}
```

7、定义一维数组

栗子1：对10个数组元素一次赋值为0～9，并按倒序输出

```c
#include <stdio.h>
int main(){
    int i,a[10] = {0,1,2,3,4,5,6,7,8,9};
    for (i = 9; i>=0; i--) {
        printf("%d\t",a[i]);
    }
    printf("\n");
    return 0;
}
```

栗子2：对`35 96 83 72 24`小到大排序并输出排序后的结果

选择排序

```c
#include <stdio.h>
int main(){
    int t,array[5] = {35,96,83,72,24};
    for (int i = 0; i < 5 ; i++) {
        for (int j = i + 1; j < 5; j++) {
            if (array[i] > array[j]) {
                t = array[i];
                array[i] = array[j];
                array[j] = t;
            }
        }
    }
    for (int i = 0; i <= 4; i++) {
        printf("%d\t",array[i]);
    }
    printf("\n");
    return 0;
}
```

冒泡排序

```c
#include <stdio.h>
int main(){
    int t,array[5] = {35,96,83,72,24};
    for (int i = 0; i < 5 ; i++) {
        for (int j = 0; j < 5; j++) {
            if (array[j] > array[j+1]) {
                t = array[j];
                array[j] = array[j+1];
                array[j+1] = t;
            }
        }
    }
    for (int i = 0; i <= 4; i++) {
        printf("%d\t",array[i]);
    }
    printf("\n");
    return 0;
}
```

8、定义二维数组

栗子1：讲一个二维数组的行列元素互换，存到另一个二维数组b中并输出。

```c
#include <stdio.h>
int main(){
    int a[2][3] = {{1,2,3},{4,5,6}};
    int b[3][2];
    for (int i=0; i<=1; i++) {
        for (int j = 0; j <= 2; j++) {
            b[j][i] = a[i][j];
        }
    }
    printf("数组b为：\n");
    for (int j = 0; j <= 2; j++) {
        for (int i = 0; i <= 1; i++) {
            printf("%d\t",b[j][i]);
        }
    }
    printf("\n");
    return 0;
}
```

> 注：当i=0时依次把a的值赋给了b的 第一列，当i=1时依次赋值给b的第二列

栗子2：已知矩阵三行三列`{{1,2,3},{9,8,7},{-10,10,-5}}`，请编程求出其中最大的那个元素。

```c
#include <stdio.h>
int main(){
    int a[3][3] = {{1,2,3},{9,8,7},{-10,100,-5}};
    int max = a[0][0];
    for (int i = 0; i <= 2; i++) {
        for (int j = 0; j <= 2; j++) {
            if (a[i][j] > max) {
                max = a[i][j];
            }
        }
    }
    printf("max最大元素=%d\n",max);
    return 0;
}
```

栗子3：输出字符数组（挨个输出）

```c
#include <stdio.h>
int main(){
    char a[15] = {'H','e','l','l','o',' ','w','o','r','l','d'};
    printf("%s\n",a);
    return 0;
}
```

栗子4：输入一行有空格 和单词组成的字符（字符数在80以内），请统计有多少个单词。

```c
#include <stdio.h>
int main(){
    char c[81];
    int dancishu = 1;
    printf("请输出：\n");
    gets(c);
    if(c[0] == ' ') {
        dancishu = 0;
    }
    for (int i = 0; c[i] !='\0'; i++) {
        if (c[i] ==' ' && c[i+1] !=' ' && c[i+1] != '\0') {
            dancishu++;
        }
    }
    printf("统计数为：%d\n",dancishu);
    return 0;
}
```

9、函数

栗子1：有两组分别5名学生和10名学生，请编程输出这些学生的成绩，并调用一个aver函数求这两个小组的平均分。

```c
#include <stdio.h>
int main(){
    float aver(float a[],int n);
    float zu1[5],zu2[10];
    int i;
    printf("请输入第一组学生成绩：\n");
    for (i = 0; i <= 4; i++) {
        scanf("%f",&zu1[i]);
    }
    printf("请输入第二组学生成绩：\n");
    for (i = 0; i <= 9; i++) {
        scanf("%f",&zu2);
    }
    printf("第一组平均分是：%f\n",aver(zu1,5));
    printf("第二组平均分是：%f\n",aver(zu2,10));
    return 0;
}
float aver(float a[], int n) {
    float sum = a[0],pingjunshu;
    int i;
    for (i = 1; i < n; i++) {
        sum = sum + a[i];
    }
    pingjunshu = sum / n;
    return (pingjunshu);
}
```

改写使用指针

```c
函数声明：数组名[]->*p
函数解释：数组名[i]->*(p+i)
函数使用：数组名->q
形参是指针变量，实参是数组名
```



10、指针1

**指针变量与一维数组**

栗子1：一个整型数组里的5个元素由键盘输入，将每个元素变为原来的两倍后依次输出

```c
#include <stdio.h>
int main(){
    int i,*p,a[5];
    p = &a[0];//&数组名[某数]相当于：数组名+某数
    printf("请输入5个整数型：\n");
    for (i = 0; i < 5; i++) {
        scanf("%d",p+i);
        *(p+i) = *(p+i)*2;
    }
    printf("加倍后的整数为：\n");
    for (i = 0; i < 5; i++) {
        printf("%d\t",*(p+i));
        printf("%d\t",(p+i));
    }
    printf("\n");
    return 0;
}
```

**指针变量与二维数组**

> 指针=&数组名`[数a][数b]`
>
> *指针=指针指定的变量的值
>
> 指针+i=&(数组名`[数a][数b]后面第i个元素`)

11、指针2

看下列所定义的变量与指针

```c
int a = 100,b = -9;
float c = 3.14;
int *d = &a;
float *e = &c;
```

解读上述：

* 变量d和e都被声明为指针，并用其他变量的地址予以初始化。指针的初始化是用&操作符完成的，它用于产生操作数的内存地址。
* 获取指针的值:`printf("获得值d:%d\n",\*d);`
* 获取指针地址：`printf("获得地址d:%d\n",d);`



链表

```c
#include <stdio.h>
typedef int SListDataType;//为了方便
//节点
struct SListNode {
  SListDataType data;
  struct SListNode* next;//指针类型还是结构体所有struct
};
```

```c
#include <stdio.h>
typedef int SListDataType;//为了方便
//节点
typedef struct SListNode {
  SListDataType data;
  struct SListNode* next;//指针类型还是结构体所有struct
}SLTNode;
```

以上就构造出一个节点了，如下图：

![image-20210513220411221](/Users/santaizi/Library/Application Support/typora-user-images/image-20210513220411221.png)

要理解链表，首先要理解链表在物理上是如何存储的？

![image-20210513222458193](/Users/santaizi/Library/Application Support/typora-user-images/image-20210513222458193.png)

我们所定义的指针就是指向下一个节点的地址。

链表操作

```c
//尾插
void SListPushBack(SListNode *phead,SListDataType x);
//尾删
void SListPopBack(SListNode *phead);
//头插
void SListPushFront(SListNode *phead,SListDataType x);
//头删
void SListPopFront(SListNode *phead);
```

```c
#include <stdio.h>
typedef int SListDataType;//为了方便
//节点
typedef struct SListNode {
  SListDataType data;
  struct SListNode* next;//指针类型还是结构体所有struct
}SLTNode;
//遍历
void SListPrint(SListNode* phead) {
  SListNode * cur = phead;
  while (cur != NULL) {
    printf("%d",cur->data);
    cur = cur->next;
  }
}

int main() {
  SListNode* pList = NULL;
  SListPrint(PList);
  return 0;
}
```

链表尾插完整代码

```c
#include <stdio.h>
typedef int SListDataType;
typedef struct SListNode {
  SListDataType data;
  struct SListNode* next;
}SLTNode;

//尾插
void SListPushBack(SListNode *phead,SListDataType x) {
  SListNode * tail = phead;
  while(tail->next != NULL) {
    tail = tail-> next;
  }
  //为新节点分配空间
  SListNode* newNode = (SListNode*)malloc(sizeof(SListNode));
  if (newNode == NULL) {
    printf("申请失败...\n");
    exit(-1);
  }
  //插入新的数据指向新的节点
  newNode -> data = x;
  //新的节点指针设为空，因为是插入最后的
  newNode -> next = NULL;
  //将刚才遍历找到原来最后一个节点的指针指向新的节点
  tail -> next = newNode;
}
int main() {
  SListNode* pList = NULL;
  SListPushBack(pList,1);
  SListPushBack(pList,2);
  return 0;
}
```



```c
#endif /* test_h */
typedef int SListDataType;
typedef struct SListNode {
    SListDataType data;
    struct SListNode* next;
}SListNode;
SListNode* BuySListNode(SListDataType x) {
  SListNode* newNode = (SListNode*)malloc(sizeof(SListNode));
    if (newNode == NULL) {
        printf("申请失败...\n");
        exit(-1);
    }
    newNode -> data = x;
    newNode -> next = NULL;
  return newNode;
}
//尾插
void SListPushBack(SListNode *phead,SListDataType x) {
    //为新节点分配空间
    SListNode* newNode = BuySListNode(x);
  if (phead == NULL) {
    phead = newNode;
  }
  else {
    SListNode * tail = phead;
    while(tail->next != NULL) {
        tail = tail-> next;
    }
    tail -> next = newNode;
  }
}

```

![image-20210515175957342](/Users/santaizi/Library/Application Support/typora-user-images/image-20210515175957342.png)

尾删

```c
//尾删
void SListPopBack(SListNode **pphead){
  //1 NULL
  //2 一个节点
  //3 一个以上节点 
  if (*pphead == NULL) {
    return;
  }
  else if ((*pphead) -> next == NULL) {
    free(*pphead);
    *pphead = NULL;
  }
  else {
    SListNode* prev = NULL;
    SListNode* tail = *pphead;
    while (tail -> next != NULL) {
      prev = tail;
      tail = tail -> next;
    }
    free(tail);
    prev-next = NULL;
  }
}

int main() {
  SListNode* pList = NULL;
  SListPushBack(pList,1);
  SListPushBack(pList,2);
  SListPopBack(&pList);
  SListPopBack(&pList);
  return 0;
}
```

