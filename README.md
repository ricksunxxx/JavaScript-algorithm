# JavaScript-algorithm
前端算法


# 1、最接近的三数之和
给你一个长度为 n 的整数数组 nums 和 一个目标值 target。请你从 nums 中选出三个整数，使它们的和与 target 最接近。
返回这三个数的和。
假定每组输入只存在恰好一个解。
```javascript {0,100}
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var threeSumClosest = function(nums, target) {
    nums.sort((a, b) => a - b); // 先排序
    let closestSum = Infinity;  // 记录最接近 target 的三数之和

    for (let i = 0; i < nums.length - 2; i++) {
        let left = i + 1, right = nums.length - 1;

        while (left < right) {
            let sum = nums[i] + nums[left] + nums[right];

            // 如果找到完全匹配的 target，直接返回
            if (sum === target) return sum;

            // 更新最接近的和
            if (Math.abs(sum - target) < Math.abs(closestSum - target)) {
                closestSum = sum;
            }

            // 移动指针
            if (sum > target) {
                right--;
            } else {
                left++;
            }
        }
    }

    return closestSum;
};
```
时间复杂度：O(n^2) 外层循环是 O(n) 内层双指针遍历是 O(n)（每次最多走一次
空间复杂度：O(1) 排序用了 O(log n) 的栈空间（如果不算也可以近似认为是 O(1)） 没有额外开数组或结构

# 2、无重复字符的最长子串
给定一个字符串 s ，请你找出其中不含有重复字符的 最长 子串 的长度。  
示例 1:  
输入: s = "abcabcbb"  
输出: 3   
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。  
```javascript
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function (s) {
    let len = 0;
    let left = 0;
    let map = new Map()
    for (let right = 0; right < s.length; right++) {
        if (map.has(s[right])) {
            left = Math.max(left, map.get(s[right]) + 1)
        }
        map.set(s[right], right)
        len = Math.max(len, right - left + 1)
    }

    return len
};

```
时间复杂度：O(n)：left 和 right 都只移动一次。  
空间复杂度：O(n)： 用于存储窗口内的字符  

# 3、分隔链表
给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。  
你应当 保留 两个分区中每个节点的初始相对位置。  
例如：  
输入：head = [1,4,3,2,5,2], x = 3  
输出：[1,2,2,4,3,5]  
```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} x
 * @return {ListNode}
 */
var partition = function (head, x) {
    let smallDummy = new ListNode(0); // 小于x的链表虚拟头
    let largeDummy = new ListNode(0); // 大于等于x的链表虚拟头
    let small = smallDummy;
    let large = largeDummy;

    let current = head;
    while (current) {
        if (current.val < x) {
            small.next = current;
            small = small.next;
        } else {
            large.next = current;
            large = large.next;
        }
        current = current.next;
    }

    // 断开 large 链表尾巴，防止成环
    large.next = null;
    // 连接两个链表
    small.next = largeDummy.next;

    return smallDummy.next;
};

```
 时间复杂度：O(n) — 单次遍历  
 空间复杂度：O(1) — 使用常数级辅助指针  

 # 4、移除元素
给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素。元素的顺序可能发生改变。然后返回 nums 中与 val 不同的元素的数量。  
假设 nums 中不等于 val 的元素数量为 k，要通过此题，您需要执行以下操作：  
更改 nums 数组，使 nums 的前 k 个元素包含不等于 val 的元素。nums 的其余元素和 nums 的大小并不重要。  
返回 k  
```javascript
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function (nums, val) {
    let i = 0;
    let n = nums.length;

    while (i < n) {
        if (nums[i] === val) {
            nums[i] = nums[n - 1]; // 用最后一个覆盖当前的
            n--; // 缩小数组有效长度
        } else {
            i++;
        }
    }
    return n;
};

```
时间复杂度：O(n) 最坏情况下，可能要遍历 n 次（数组长度）。  
空间复杂度：O(1)。 

# 5、最大频率栈
设计一个类似堆栈的数据结构，将元素推入堆栈，并从堆栈中弹出出现频率最高的元素。  
实现 FreqStack 类:FreqStack() 构造一个空的堆栈。  
void push(int val) 将一个整数 val 压入栈顶。  
int pop() 删除并返回堆栈中出现频率最高的元素。  
如果出现频率最高的元素不只一个，则移除并返回最接近栈顶的元素。  
```javascript
var FreqStack = function () {
    this.map = {};        // 存储元素的频率
    this.groupMap = {};   // 存储频率对应的元素栈
    this.maxFreq = 0;     // 当前最大频率
};

/** 
 * @param {number} val
 * @return {void}
 */
FreqStack.prototype.push = function (val) {
    // 更新元素频率
    const freq = (this.map[val] = (this.map[val] || 0) + 1);
    
    // 更新最大频率
    this.maxFreq = Math.max(this.maxFreq, freq);
    
    // 将元素加入对应频率的栈中
    if (!this.groupMap[freq]) {
        this.groupMap[freq] = [];
    }
    this.groupMap[freq].push(val);
};

/**
 * @return {number}
 */
FreqStack.prototype.pop = function () {
    // 从最大频率的栈中弹出最接近栈顶的元素
    const val = this.groupMap[this.maxFreq].pop();

    // 更新元素频率
    this.map[val]--;

    // 如果该频率的元素栈空了，更新最大频率
    if (this.groupMap[this.maxFreq].length === 0) {
        this.maxFreq--;
    }

    return val;
};

/** 
 * Your FreqStack object will be instantiated and called as such:
 * var obj = new FreqStack();
 * obj.push(val);
 * var param_2 = obj.pop();
 */
```
时间复杂度：O(1) 
空间复杂度：O(n)。

# 6、旋转链表
题目：给你一个链表的头节点 head ，旋转链表，将链表每个节点向右移动 k 个位置。  
解题思路:  
求链表长度：  
如果长度为 n，旋转 k 次，相当于旋转 k % n 次。  
闭环再断开：  
把链表变成一个环（最后一个节点指向头节点）。  
然后从新位置“断开”环，形成新的头节点。  
```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} k
 * @return {ListNode}
 */
var rotateRight = function (head, k) {
    if (!head || !head.next || k === 0) return head;

    // 第一步：计算链表长度
    let length = 1;
    let tail = head;
    while (tail.next) {
        tail = tail.next;
        length++;
    }

    // 第二步：将链表连成环
    tail.next = head;

    // 第三步：找到新的尾节点，新头节点
    k = k % length;
    let stepsToNewTail = length - k;

    let newTail = head;
    while (--stepsToNewTail > 0) {
        newTail = newTail.next;
    }

    let newHead = newTail.next;

    // 第四步：断开环
    newTail.next = null;

    return newHead;
};
```
时间复杂度：O(n)  
空间复杂度：O(1)

# 7、我的日程安排I
实现一个 MyCalendar 类来存放你的日程安排。如果要添加的日程安排不会造成 重复预订 ，则可以存储这个新的日程安排。  
当两个日程安排有一些时间上的交叉时（例如两个日程安排都在同一时间内），就会产生 重复预订 。  
日程可以用一对整数 startTime 和 endTime 表示，这里的时间是半开区间，即 [startTime, endTime), 实数 x 的范围为，  startTime <= x < endTime 。  
实现 MyCalendar 类：  
MyCalendar() 初始化日历对象。  
boolean book(int startTime, int endTime) 如果可以将日程安排成功添加到日历中而不会导致重复预订，返回 true 。否则，返回 false 并且不要将该日程安排添加到日历中  
```javascript
var MyCalendar = function () {
    this.calendar = [];
};

/**
 * @param {number} start 
 * @param {number} end
 * @return {boolean}
 */
MyCalendar.prototype.book = function (start, end) {
    for (let [s, e] of this.calendar) {
        // 判断是否有重叠
        if (start < e && s < end) {
            return false;
        }
    }
    this.calendar.push([start, end]);
    return true;
};

```
时间复杂度：O(n)  
空间复杂度：O(n)  

# 8、K个一组翻转链表
给你链表的头节点 head ，每 k 个节点一组进行翻转，请你返回修改后的链表。  
k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。  
你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。  
```javascript
function reverseKGroup(head, k) {
    // 判断链表是否有至少 k 个节点
    let count = 0;
    let node = head;

    // 计算链表的长度
    while (node && count < k) {
        node = node.next;
        count++;
    }

    // 如果剩余的节点不足 k 个，则不翻转，直接返回当前链表
    if (count < k) {
        return head;
    }

    // 翻转前 k 个节点
    let prev = null;
    let curr = head;
    for (let i = 0; i < k; i++) {
        let nextTemp = curr.next;
        curr.next = prev;
        prev = curr;
        curr = nextTemp;
    }

    // 递归调用处理剩下的部分
    // head 是当前翻转后的部分的尾节点，curr 是下一个待翻转部分的头节点
    head.next = reverseKGroup(curr, k);

    // 返回翻转后的新头节点
    return prev;
}

```
时间复杂度：O(n)  
空间复杂度：O(n / k)（递归调用栈）  
# 9、砌砖
你的面前有一堵矩形的、由 n 行砖块组成的砖墙。这些砖块高度相同（也就是一个单位高）但是宽度不同。每一行砖块的宽度之和相等。  
你现在要画一条 自顶向下 的、穿过 最少 砖块的垂线。如果你画的线只是从砖块的边缘经过，就不算穿过这块砖。你不能沿着墙的两个垂直边缘之一画线，这样显然是没有穿过一块砖的。  
给你一个二维数组 wall ，该数组包含这堵墙的相关信息。其中，wall[i] 是一个代表从左至右每块砖的宽度的数组。你需要找出怎样画才能使这条线 穿过的砖块数量最少 ，并且返回 穿过的砖块数量 。  
```javascript
/**
 * @param {number[][]} wall
 * @return {number}
 */
var leastBricks = function (wall) {
    let map = {}
    for (let arr of wall) {
        let sum = 0
        for (let i = 0; i < arr.length - 1; i++) {
            sum = sum + arr[i]
            map[sum] = (map[sum] || 0) + 1
        }
    }

    let max = 0
    for (let k of (Object.keys(map))) {
        max = Math.max(max, map[k])
    }

    return wall.length - max;
}
```
时间复杂度：O(n × m)    
空间复杂度：O(n × m)（最坏）
