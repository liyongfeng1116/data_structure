# 栈（Stack）
##栈的简介
1. 栈是**后进先出**的
2. 只能在栈顶操作元素
3. 每次只能弹出一个元素pop()；或者压入一个元素push()；或者查看一个元素peek();
4. 栈的压元素和取元素跟递归有相通之处
## Stack的基本操作
1. 初始化  
2. 判断栈是否为空  
3. 进栈，判断栈是否已满
4. 出栈，判断栈是否为空
5. 读栈顶元素，判断是否为空
6. 销毁栈

## 基础题目
1. 括号匹配（[leetcode20](https://leetcode-cn.com/problems/valid-parentheses/)）
```
class Solution {
    public boolean isValid(String s) {
        // 依次入栈，当栈顶元素和即将入栈元素匹配时，弹出原来的栈顶元素
        // 如果不匹配，入栈该元素
        // 最后如果栈为空，则返回true
        int n = s.length();
        if (n % 2 == 1) {
            return false; //奇数个的话肯定不能完全消除
        }
        // 以右边括号为key 左边括号为value
        Map<Character, Character> map = new HashMap<Character, Character>();
        map.put(')','(');
        map.put(']','[');
        map.put('}','{');
        Deque<Character> stack = new LinkedList<Character>();
        for (int i = 0; i < n; i++) {
            char ch = s.charAt(i); // 待处理元素 charAt(i)返回字符中的下标为i的元素
            if (map.containsKey(ch)) {
                // 取出的是右括号时，如果栈里为空或者不能和栈顶匹配
                if (stack.isEmpty() || stack.peek() != map.get(ch)) {
                    return false;
                }else {
                    stack.pop();
                }
            }else {  // 左括号直接入栈
                stack.push(ch);
            }
        }
        return stack.isEmpty();
    }
}
```
2. 回文链表（[leetcode 027](https://leetcode-cn.com/problems/aMhZSa/)）**还有其他方法**

```
class Solution {
    public boolean isPalindrome(ListNode head) {
        // 所有节点入栈，出栈顺序相当于倒过来，比较每个值
        if (head == null || head.next == null) {
            return true;
        }
        Deque<Integer> stack = new LinkedList<Integer>();
        ListNode curr = head;
        while (curr != null) {
            stack.push(curr.val);
            curr = curr.next;
        }
        for (int i = 0; i < stack.size(); i++) { //栈的大小stack.size()
            if (head.val != stack.pop()) {
                return false;
            }
            head = head.next;
        }
        return true;
    }
}
```
## 处理环形数组
有些问题需要处理环形数组，这种数组头尾相接。一般通过**求模运算**来做。
nums = [2,0,6,1,3] 的环形数组就是将其拉伸为[2,0,6,1,3,2,0,6,1] 数组长度为5，则最后一个元素的下标为8 % 5 = 3 
## 重点题型
### 单调栈
- 单调栈内的元素都是递增或者递减的
- 一般用来处理**下一个更大的问题**
- 从后往前扫描，
```
**************************单调栈模板**************************
int n = nums.length;
int [] res = new int[n];
Deque<Integer> stack = new LinkedList<Integer>();
for (int i = n - 1; i >= 0; i--) {
    while (!stack.isEmpty() && stack.peek() <= nums[i]) {
        stack.pop();
    }
    if (stack.isEmpty()) {
        res[i] = -1;
    } else {
        res[i] = stack.peek();
    }
    stack.push(nums[i]);
}
```
1. 下一个更大元素（[leetcode 496](https://leetcode-cn.com/problems/next-greater-element-i/)） 相似的题（[leetcode 503](https://leetcode-cn.com/problems/next-greater-element-i/)）
```
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int size = nums.length;
        int[] res = new int[size];
        Deque<Integer> stack = new LinkedList<Integer>();
        // 数组长度本来为size，给其翻倍,从后往前入栈
        for (int i = 2 * size - 1; i >= 0; i--) {
            // 将栈里小的弹出，因为被后面进来的遮挡住
            while (!stack.isEmpty() && stack.peek() <= nums[i % size]) {
                stack.pop();
            }
            if (stack.isEmpty()) {
                res[i % size] = -1;
            } else {
                res[i % size] = stack.peek();   
            }
            stack.push(nums[i % size]);
        }
        return res;
    }
}

```
```
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int n1 = nums1.length;
        int n2 = nums2.length;
        int[] ans = new int[n1];
        Map<Integer,Integer> map = new HashMap<Integer,Integer>();
        Deque<Integer> stack = new LinkedList<Integer>();
        for (int i = n2 - 1; i >= 0; i--) {
            while (!stack.isEmpty() && stack.peek() <= nums2[i]) {
                stack.pop();
            }
            if (stack.isEmpty()) {
                map.put(nums2[i], -1);
            } else {
                map.put(nums2[i], stack.peek());
            }
            stack.push(nums2[i]);
        }
        for (int i = 0; i < n1; i ++) {
            ans[i] = map.get(nums1[i]);
        }
        return ans;
    }
}
```
2. 每日温度 （[leetcode 739](https://leetcode-cn.com/problems/daily-temperatures/)）
- 用索引表示更加通用
```
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] res = new int[n];
        Deque<Integer> stack = new LinkedList<Integer>();
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && temperatures[stack.peek()] <= temperatures[i]) {
                stack.pop();
            }
            if (stack.isEmpty()) {
                res[i] = 0;
            } else {
                res[i] = stack.peek() - i;
            }
            stack.push(i);
        }
        return res;
    }
}
```
3. 链表中两数相加（[leetcode445](https://leetcode-cn.com/problems/add-two-numbers-ii/)）
- 最高位在最左边，要想相加，必须从个位开始
- 考虑进位
```
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        Deque<Integer> stack1 = new LinkedList<Integer>();
        Deque<Integer> stack2 = new LinkedList<Integer>();

        while (l1 != null) {
            stack1.push(l1.val);
            l1 = l1.next;
        }
        while (l2 != null) {
            stack2.push(l2.val);
            l2 = l2.next;
        }

        int flag = 0;
        ListNode res = null;
        while (!stack1.isEmpty() || !stack2.isEmpty() || flag != 0) {
            int num1; // 需要外面先申明，然后按照要求赋值
            int num2;
            if (!stack1.isEmpty()) {
                num1 = stack1.pop();
            } else {
                num1 = 0;
            }
            if (!stack2.isEmpty()) {
                num2 = stack2.pop();
            } else {
                num2 = 0;
            }
            int temp = num1 + num2 + flag;
            flag = temp / 10;
            int curr = temp % 10;
            ListNode currnode = new ListNode(curr);
            currnode.next = res; // 第一个指向空,剩下的每一个指向前一个
            res = currnode;
        }
        return res;
    }
}
```
**赋值的简单写法：** *三目运算符*
`int a = stack.isEmpty() ? 0 : stack.pop();`

4. 接雨水（[leetcode42](https://leetcode-cn.com/problems/trapping-rain-water/)） 相似题目（[leetcode84](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)）



 

