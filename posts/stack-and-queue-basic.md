---
title: 栈与队列的基础实现
date: 2018-02-02 00:38:26
tags: [OI,C++,数据结构,线性表]
toc: true
---
之前学习栈和队列就不是很系统，这会趁着有时间赶紧补一补。

## 栈
栈 (stack) 是一种“先入后出”的数据结构。栈的基本结构类似洗盘子，你必须把上面的盘子全部移走，才能够取得下面的盘子。栈的图解如下:
![stack picture](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201802/stack.png)  
可以看出，栈由两部分组成，数据和栈指针。  
在 C++ 中，我们一般通过数组模拟来实现栈。  
栈的类型声明如下:  
~~~ cpp
typedef struct {
	int data[MAXN];
	int top;
} stack;
~~~ 
栈的一些基本操作包括: 压入、弹出。  
我们现在就开始编写一些与栈有关的函数。
### 初始化
栈的初始化很有意思，一般我们在初始化时将栈指针设置为 -1。  
~~~ cpp
void stack_init(stack *s) {
	s->top = -1;
}
~~~ 
因为接下来我们将数据压入栈的时候，栈指针会先自己加 1，因此将栈指针设置为 -1 可以让第一个元素的指针为 0。  
<span style="color:orange">这里说的“指针”，和 C++ 中常规的指针不同，但其存在的根本意义是一致的。</span>
### 判断栈空或满
参照我们初始化栈时的说明，当栈指针等于 -1 时，栈一定是空的。  
~~~ cpp
bool stack_empty(stack *s) {
	return (s->top == -1) ? true : false;
}
~~~   
如何判断栈是否满了呢？我们知道，这里栈使用数组模拟，那么如果栈指针等于数组的最大下标，就意味着栈已经满了。
~~~ cpp
bool stack_full(stack *s) {
	return (s->top == MAXN - 1) ? true : false;
}
~~~ 

### 栈的压入
由于存在栈已满的情况，因此我们不能假定操作一定成功，所以函数的返回值应为布尔型。  
首先应当判断栈是否已满，如果已满肯定是压不进去的。如果没有满，意味着可以压入数据。我们可以直接调用之前实现的函数。  
压入数据时，首先让栈指针上移一位，然后将栈指针当前指向的栈空间赋值。  
~~~ cpp
bool stack_push(stack *s, int a) {
	if (!stack_full(s)) {
		s->top++;
		s->data[s->top] = a;
		return true;
	} 
	else return false;
}
~~~ 

### 栈的弹出
同理，栈可能是空的，如果栈是空的，我们也无法弹出任何数据。  
栈的弹出操作可以看做是压入操作的逆运算。因此，这里要先取出数据，再将栈指针下移。
~~~ cpp
bool stack_pop(stack *s, int *a) {
	if (!stack_empty(s)) {
		*a = s->data[s->top];
		s->top--;
		return true;
	}
	else return false;
}
~~~ 
那么为什么这一次，我们要将第二个形参设置为 \*a 而不是 a 呢？因为弹出操作需要给变量赋值，而不是取值。因此这里我们使用 \* 就是为了让函数操作其作用域之外的但是被传入的变量。

### 获取栈顶数据
这个非常简单，只要当前栈非空，那么栈指针指向的数据一定是栈顶数据。  
~~~ cpp
bool stack_getTop(stack *s, int *a) {
	if (!stack_empty(s)) {
		*a = s->data[s->top];
		return true;
	}
	else return false;
}
~~~ 

### 获取栈的有效长度
这个更是简单至极，甚至无需判断栈是否非空，因为栈的第一个元素指针为 0，所以栈的长度一定是栈指针加上 1。而栈为空时，由于此时栈指针为 -1，$-1 + 1 =0$，完美符合栈空时的长度。(是不是觉得将栈的初始指针设为 -1 很妙啊)  
~~~ cpp
int stack_length(stack *s) {
	return s->top + 1;
}
~~~ 

### 栈的遍历
我们知道，栈是先进后出的数据结构，而且是一个操作受限的线性表。  
因此，我们要遍历一个栈，只要依次获取当前栈指针指向的数据，再将栈指针下移。  
我们可以通过栈的弹出操作来简化这一过程:  
~~~ cpp
void stack_traversal(stack *s) {
	int i = 0;
	while (!stack_empty(s)) {
		stack_pop(s, &i);
		printf("%d ", i);
	}
	putchar('\n');
}
~~~ 

### 示例程序
这个例子展示了输入数据压入栈中，再获取栈长度、栈顶数据，然后对栈遍历的操作。  
~~~ cpp
#include <cstdio>

const int MAXN = 100 + 1;
typedef struct {
	int data[MAXN];
	int top;
} stack;
void stack_init(stack *s) {
	s->top = -1;
}
bool stack_empty(stack *s) {
	return (s->top == -1) ? true : false;
}
bool stack_full(stack *s) {
	return (s->top == MAXN - 1) ? true : false;
}
bool stack_push(stack *s, int a) {
	if (!stack_full(s)) {
		s->top++;
		s->data[s->top] = a;
		return true;
	} 
	else return false;
}
bool stack_pop(stack *s, int *a /* Because we need to change variable a */) {
	if (!stack_empty(s)) {
		*a = s->data[s->top];
		s->top--;
		return true;
	}
	else return false;
}
bool stack_getTop(stack *s, int *a) {
	if (!stack_empty(s)) {
		*a = s->data[s->top];
		return true;
	}
	else return false;
}
int stack_length(stack *s) {
	return s->top + 1;
}

void stack_traversal(stack *s) {
	int i = 0;
	while (!stack_empty(s)) {
		stack_pop(s, &i);
		printf("%d ", i);
	}
	putchar('\n');
}
int amount;
int main() {
	stack a;
	stack_init(&a);
	scanf("%d", &amount);
	for (int i = 0, tmpVar = 0; i < amount; i++) {
		scanf("%d", &tmpVar);
		stack_push(&a, tmpVar);
	}
	int top;
	stack_getTop(&a, &top);
	printf("Valid length: %d, Top element: %d\n", stack_length(&a), top);
	printf("Traversal: \n");
	stack_traversal(&a);
	return 0;
}
~~~ 
## 队列
和栈不同的是，队列 (queue) 是一个“先入先出”的数据结构。你可以把它想象成食堂排队打饭的队列，没有人中途离开 (想离开的都饿死了)，而且不存在插队的情况 (因为插队的都被我们打死了)。  
这种情况下，在队头的人打上饭离开了，这就叫“出队”。打酱油的人看到前面的人出队了。于是跟进队尾，这就叫“入队”。  
队列的图解如下:
![queue picture](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201802/queue.png)
<span style="color: orange">这里我们不讨论循环队列和链表队列等等，我们只讨论最基础的队列。</span>  
在 C++ 中，我们同样通过数组模拟来实现队列。  
~~~ cpp
typedef struct {
	int data[MAXN];
	int front;
	int rear;
} queue;
~~~ 
队列的基本操作包括: 入队、出队。  
现在我们就来编写一些和队列有关的函数。  
### 初始化
队列的初始化更加有趣，一般我们把队头指针设置为 0，队尾指针设置为 -1。这当然是有原因的，看下去你就会明白。
~~~ cpp
void queue_init(queue *q) {
	q->front = 0;
	q->rear = -1;
}
~~~ 

### 判断队列为空或满
我们知道，队列是一个只能从前面出队，从后面入队的。也就是说出队操作意味着队头指针的下移，入队操作意味着队尾指针的下移。  
因此，如果队头指针不断下移，直至移到了比队尾指针还要靠下，那么这个队列必然是空的了。(相等时意味着这是只有一个元素的队列)  
或者是另一种情况，例如在初始化时，队头指针为 0 而队尾指针为 -1，出现了队尾指针在队头指针上方的情况，这种情况自然也不存在合法的队列了。  
归纳，我们可以得到，当队头指针在队尾指针下方时，这个队列一定为空。  
~~~ cpp
bool queue_empty(queue *q) {
	if (q->front > q->rear) {
		return true;
	}
	else return false;
}
~~~ 
那么我们如何判断队列是否已经满了呢？目前来说，可以暂时看做和栈是一致的。
~~~ cpp
bool queue_full(queue *q) {
	if (q->rear == MAXN - 1) {
		return true;
	}
	else return false;
}
~~~ 
但事实上，由于出队时队头指针数字的增加，导致虽然看上去队列满了，其实数组中还有空间没有被利用。我们可以通过循环数组等方式来解决这种“假溢出”问题。但这不在本文的讨论范围之内。  

### 队列的入/出队操作和获取队头/尾元素
前面我们已经知道了栈的压入和弹出操作，队列的也类似。只不过入队只能从队尾入队，出队只能从队头出队罢了。
~~~ cpp
bool queue_in(queue *q, int a) {
	if (!queue_full(q)) {
		q->rear++;
		q->data[q->rear] = a;
		return true;
	}
	else return false;
}

bool queue_out(queue *q, int *a) {
	if (!queue_empty(q)) {
		*a=q->data[q->front];
		q->front++;
		return true;
	}
	else return false;
} 
~~~ 
获取队头队尾元素的方法可以参考栈的说明。
~~~ cpp
bool queue_getFront(queue *q, int *a) {
	if (!queue_empty(q)) {
		*a=q->data[q->front];
		return true;
	}
	else return false;
}

bool queue_getRear(queue *q, int *a) {
	if (!queue_empty(q)) {
		*a=q->data[q->rear];
		return true;
	} 
	else return false;
}
~~~ 

### 队列的长度
如果你面前有一个队列，里面第一个人的编号为 1，最后一个人的编号为 6。你能一眼看出这个队列有 6 个人。  
显然，$L =n_{rear} - n_{front} + 1$。  
~~~ cpp
int queue_length(queue *q) {
	return q->rear - q->front + 1;
}
~~~ 

### 队列的遍历
队列的遍历和栈的遍历类似，只是将弹出操作换成了出队操作罢了。   
~~~ 
void queue_traversal(queue *q) {
	int i = 0;
	while (!queue_empty(q)) {
		queue_out(q, &i);
		printf("%d ", i);
	}
	putchar('\n');
}
~~~ 

### 示例程序
此程序所实现的功能和栈的那个示例类似。
~~~ cpp
#include <cstdio>
const int MAXN = 100 + 1;
typedef struct {
	int data[MAXN];
	int front;
	int rear;
} queue;

void queue_init(queue *q) {
	q->front = 0;
	q->rear = -1;
}

bool queue_empty(queue *q) {
	if (q->front > q->rear) {
		return true;
	}
	else return false;
}

bool queue_full(queue *q) {
	if (q->rear == MAXN - 1) {
		return true;
	}
	else return false;
}

bool queue_in(queue *q, int a) {
	if (!queue_full(q)) {
		q->rear++;
		q->data[q->rear] = a;
		return true;
	}
	else return false;
}

bool queue_out(queue *q, int *a) {
	if (!queue_empty(q)) {
		*a=q->data[q->front];
		q->front++;
		return true;
	}
	else return false;
} 

bool queue_getFront(queue *q, int *a) {
	if (!queue_empty(q)) {
		*a=q->data[q->front];
		return true;
	}
	else return false;
}

bool queue_getRear(queue *q, int *a) {
	if (!queue_empty(q)) {
		*a=q->data[q->rear];
		return true;
	} 
	else return false;
}

int queue_length(queue *q) {
	return q->rear - q->front + 1;
}

void queue_traversal(queue *q) {
	int i = 0;
	while (!queue_empty(q)) {
		queue_out(q, &i);
		printf("%d ", i);
	}
	putchar('\n');
}
int amount;
int main() {
	queue a;
	queue_init(&a);
	scanf("%d", &amount);
	for (int i = 0, tmpVar = 0; i < amount; i++) {
		scanf("%d", &tmpVar);
		queue_in(&a, tmpVar);
	}
	int front, rear;
	queue_getFront(&a, &front);
	queue_getRear(&a, &rear);
	printf("Valid length: %d, Front element: %d, Rear element: %d\n", queue_length(&a), front, rear);
	printf("Traversal: \n");
	queue_traversal(&a);
	return 0;
}

~~~ 