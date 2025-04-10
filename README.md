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

# 10、从前序和中序遍历序列构造二叉树。
给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。  

```javascript
class TreeNode {
    constructor(val) {
        this.val = val;
        this.left = this.right = null;
    }
}

function buildTree(preorder, inorder) {
    // 用哈希表加速 index 查找
    const inorderIndexMap = new Map();
    inorder.forEach((val, idx) => inorderIndexMap.set(val, idx));

    let preIndex = 0;

    function build(left, right) {
        if (left > right) return null; // 没有节点了

        // 当前根节点值
        const rootVal = preorder[preIndex++];
        const root = new TreeNode(rootVal);

        // 根节点在中序中的位置
        const index = inorderIndexMap.get(rootVal);

        // 递归构建左右子树
        root.left = build(left, index - 1);
        root.right = build(index + 1, right);

        return root;
    }

    return build(0, inorder.length - 1);
}
```
时间复杂度：O(n)	每个节点处理一次，使用哈希加速查找  
空间复杂度：O(n)	递归栈 + 哈希表（Map）


# 11、路径总和
给你二叉树的根节点 root 和一个表示目标和的整数 targetSum 。  
判断该树中是否存在 根节点到叶子节点 的路径，这条路径上所有节点值相加等于目标和 targetSum 。  
如果存在，返回 true ；否则，返回 false 。  
叶子节点 是指没有子节点的节点。
```javascript
function hasPathSum(root, targetSum) {
    if (!root) return false;

    // 如果是叶子节点，判断路径和是否等于 targetSum
    if (!root.left && !root.right) {
        return root.val === targetSum;
    }

    // 递归左右子树，更新目标值
    const newTarget = targetSum - root.val;
    return hasPathSum(root.left, newTarget) || hasPathSum(root.right, newTarget);
}

```
时间复杂度：O(n)每个节点访问一次  
空间复杂度：O(h)h 是树的高度（递归栈），最坏 O(n)，最好 O(log n)

# 12、验证二叉搜索树
给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。  
有效 二叉搜索树定义如下：  
节点的左子树只包含 小于 当前节点的数。  
节点的右子树只包含 大于 当前节点的数。  
所有左子树和右子树自身必须也是二叉搜索树。    

解题思路：递归 + 限定上下界  
核心思想：  
对于每个节点 node，它的值必须在 (min, max) 区间内  
左子树的合法范围是 (min, node.val)  
右子树的合法范围是 (node.val, max)  
初始时，对根节点来说，min = -Infinity，max = Infinity  
```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {boolean}
 */
var isValidBST = function (root) {
    function valid(root, left, right) {
        if (!root) return true
        if (root.val <= left || root.val >= right) return false

        return (valid(root.left, left, root.val) && valid(root.right, root.val, right))
    }

    return valid(root, -Infinity, Infinity)
};
```
时间复杂度：O(n) 每个节点访问一次  
空间复杂度：O(h) h 是树的高度（递归栈空间） 

这个题其实还要第二种解法：中序遍历 + 判断是否严格递增，因为中序遍历的结果一定是严格递增的。  
按照「中序遍历」访问每个节点  
记录上一个访问的节点值 prev  
如果当前节点值 <= prev，说明不合法，返回 false  
如果遍历完整棵树都没出错，说明是合法 BST  

```javascript
var isValidBST = function (root) {
    let prev = null;

    function inOrder(node) {
        if (!node) return true;

        if (!inOrder(node.left)) return false;

        if (prev !== null && node.val <= prev) {
            return false;
        }

        prev = node.val;

        return inOrder(node.right);
    }

    return inOrder(root);
};
```
时间复杂度：O(n) 每个节点访问一次  
空间复杂度：O(h) 递归栈深度，h 是树高，最坏 O(n)，最好 O(log n)  

# 13、爱吃香蕉的珂珂
珂珂喜欢吃香蕉。这里有 n 堆香蕉，第 i 堆中有 piles[i] 根香蕉。警卫已经离开了，将在 h 小时后回来。  
珂珂可以决定她吃香蕉的速度 k （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 k 根。如果这堆香蕉少于 k 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。    
珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。  
返回她可以在 h 小时内吃掉所有香蕉的最小速度 k（k 为整数）。
```JavaScript
/**
 * @param {number[]} piles
 * @param {number} h
 * @return {number}
 */
var minEatingSpeed = function (piles, h) {
    let left = 1
    let right = Math.max(...piles)

    function canfinish(speed) {
        let times = 0
        for (let k of piles) {
            times += Math.ceil(k / speed)
        }
        return times <= h
    }

    while (left < right) {
        let mid = Math.floor((right + left) / 2)
        if (canfinish(mid)) {
            right = mid
        } else {
            left = mid + 1
        }
    }

    return left
};
```
时间复杂度：O(n⋅log(max(piles)))  
空间复杂度：O(1) 

# 14、分糖果
小明从糖果盒中随意抓一把糖果，每次小明会取出一半的糖果分给同学们。  
当糖果不能平均分配时，小明可以选择从糖果盒中（假设盒中糖果足够）取出一个糖果或放回一个糖果。  
小明最少需要多少次（取出、放回和平均分配均记一次），能将手中糖果分至只剩一颗。  

解题思路：贪心算法  
我们需要从 n 变成 1，而每一步可以是：  
如果当前糖果数是偶数：直接除以 2；  
如果当前糖果数是奇数：可以选择 +1 或 -1，使其变成偶数，然后再进行除以 2。  
我们想的是 最少步数，所以每次对奇数该加 1 还是减 1，要做一个“贪心”决策：  
如果 n == 3 或 n % 4 == 1，选择 n -= 1；  
否则选择 n += 1； 这样做是为了更快地进入能连续除以 2 的状态（能除以更多次 2，步数会更少）。  
```JavaScript
function minStepsToOne(n) {
  let steps = 0;
  while (n > 1) {
    if (n % 2 === 0) {
      n = n / 2;
    } else {
      // 贪心选择加1或减1
      if (n === 3 || n % 4 === 1) {
        n -= 1;
      } else {
        n += 1;
      }
    }
    steps++;
  }
  return steps;
}
```
时间复杂度：O(log n)   
空间复杂度：O(1) 

