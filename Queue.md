# 队(Queue)
## 队的基本概念
1. 一个入口(队尾),一个出口(队头)
2. 先进先出
*排队系统;挂号系统等*
## 队列的基本操作
1. 判断是否为空队列——头尾指向同处
2. 入队(判断是否已满)—— count == capacity
3. 出队(判断是否为空)—— count == 0 
4. **(循环队列)**
## 基础题目
1. 循环队列([leetcode 622](https://leetcode-cn.com/problems/design-circular-queue/))
- 任何数据结构都不存在环型结构.
- **一维数组模拟环形结构**
*tailIndex = (headIndex + count - 1) mode capacity*
*tailIndex*为尾指针的索引;*headIndex*为头指针的索引;*count*为队列长度;*capacity*为数组容量.
- 需要4个参数，queue，headIndex，count，capacity，*tailIndex*通过计算得出。
![循环链表](https://pic.leetcode-cn.com/Figures/622/622_queue_with_array.png "循环链表")
```
class MyCircularQueue {
    // tailIndex = (headIndex + count - 1) mode capacity
    // 需要headIndex, count, 和一个数组
    private int[] queue;
    private int headIndex;
    private int count;
    private int capacity;
    public MyCircularQueue(int k) {
        this.capacity = k;
        this.queue = new int[k];
        this.headIndex = 0;
        this.count = 0;
    }
    
    public boolean enQueue(int value) {
        // 插入前判断是否已满
        if (this.count == this.capacity) {
            return false;
        }
        this.queue[(this.headIndex + this.count) % this.capacity] = value;
        this.count += 1;
        return true;
    }
    
    public boolean deQueue() {
        // 删除判断为空
        if (this.count == 0) {
            return false;
        }
        this.headIndex = (this.headIndex + 1) % this.capacity;
        this.count -= 1;
        return true;
    }
    
    public int Front() {
        if (this.count == 0) {
            return -1;
        }
        return this.queue[this.headIndex];
    }
    
    public int Rear() {
        if (this.count == 0) {
            return -1;
        }
        return this.queue[(this.headIndex + this.count - 1) % this.capacity];
    }
    
    public boolean isEmpty() {
        if (this.count == 0) {
            return true;
        } else {
            return false;
        }
    }
    
    public boolean isFull() {
        if (this.count == this.capacity) {
            return true;
        } else {
            return false;
        }
    }
}
```
- **单链表模拟循环队列**
单链表不需要预先分配内存,效率更高.
![单链表](https://pic.leetcode-cn.com/Figures/622/622_queue_linked_list.png "Linkedlist")
```
// 创建单链表
class Node {
    public int value;
    public Node nextNode;

    public Node (int value) { // 构造方法
        this.value = value;
        this.nextNode = null;
    }
}

class MyCircularQueue {
    /*
    四个参数:
        capacity: 最大允许容量
        head: 头指针
        count: 当前队列长度
        tail: 尾指针

    */
    private Node head, tail;
    private int capacity, count;
    public MyCircularQueue(int k) {
        this.capacity = k;
    }
    
    public boolean enQueue(int value) {
        if (this.capacity == this.count) {
            return false;
        }
        Node curr = new Node(value);
        // 只有一个值时
        if (this.count == 0) {
            this.head = this.tail = curr;
        } else {
            this.tail.nextNode = curr;
            this.tail = curr;
        }  
        this.count += 1;
        return true;
    }
    
    public boolean deQueue() {
        if (this.count == 0) {
            return false;
        }
        this.head = this.head.nextNode;
        this.count -= 1;
        return true;
    }
    
    public int Front() {
        if (this.count == 0) {
            return -1;
        } else {
            return this.head.value;
        }
    }
    
    public int Rear() {
        if (this.count == 0) {
            return -1;
        } else {
            return this.tail.value;
        }
    }
    
    public boolean isEmpty() {
        if (this.count == 0) {
            return true;
        } else {
            return false;
        }
    }
    
    public boolean isFull() {
        if (this.count == this.capacity) {
            return true;
        } else {
            return false;
        }
    }
}
```
2. 用队列实现栈([leetcode 225](https://leetcode-cn.com/problems/implement-stack-using-queues/))
- 队列是两个口,**头出尾进,先进先出**
- 栈是一个口,**头出头进,后进先出**
- 为了模拟栈,栈顶操作必须在队列头,先把目标元素进入队列2,然后将原来的队列1的元素出队列进入队列2,队列2和队列1置换
```
class MyStack {
    Queue<Integer> queue1;
    Queue<Integer> queue2;
    public MyStack() {
        queue1 = new LinkedList<Integer>();
        queue2 = new LinkedList<Integer>();
    }
    //始终保持queue2为空，每次装栈顶元素，然后把queue1中的元素挨个出队，放到queue2中，此时1为空，并交换1和2
    public void push(int x) {
        // queue2加入新的元素,在队头
        queue2.offer(x);
        while (!queue1.isEmpty()) {
            queue2.offer(queue1.poll());
        }
        Queue<Integer> temp = queue1;
        queue1 = queue2;
        queue2 = temp; //queue2始终为空
    }
    
    public int pop() {
        return queue1.poll();
    }
    
    public int top() {
        return queue1.peek();
    }
    
    public boolean empty() {
        return queue1.isEmpty();
    }
}
```
3. 用栈实现队列([leetcode 232](https://leetcode-cn.com/problems/implement-queue-using-stacks/))
- 用栈模拟队列,则加入的元素应该放在栈底.
- 用一个辅助栈来先腾出其他元素.
```
class MyQueue {
    Deque<Integer> stack1;
    Deque<Integer> stack2;
    public MyQueue() {
        stack1 = new LinkedList<Integer>();
        stack2 = new LinkedList<Integer>();
    }
    
    public void push(int x) {  
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
        stack1.push(x);
        while (!stack2.isEmpty()) {
            stack1.push(stack2.pop());
        }
    }
    
    public int pop() {
        return stack1.pop();
    }
    
    public int peek() {
        return stack1.peek();
    }
    
    public boolean empty() {
        return stack1.isEmpty();
    }
}
```
1. 队列最大值([leetcode622](https://leetcode-cn.com/problems/design-circular-queue/))
2. 滑动窗口最大值([leetcode622](https://leetcode-cn.com/problems/design-circular-queue/))

