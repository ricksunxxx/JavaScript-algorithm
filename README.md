# JavaScript-algorithm
记录一些前端场景下可能需要的算法


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


# 15、dfs+剪枝
给定数组 powers，长度为 10。
目标是找出两个5人小组，使得 Math.abs(组1战力和 - 组2战力和) 最小。 

解题思路：    
从 powers 中选出 5 个人组成一组。  
总战力和为 totalSum，目标是让一组的和尽量接近 totalSum / 2。  
使用 DFS 遍历组合时：  
路径长度超过 5 剪枝。  
和已经超过一半时剪枝（不可能更优）。  
当前路径差值大于已知最小差值时剪枝。    
```JavaScript
function minTeamDiff(powers) {
  const totalSum = powers.reduce((a, b) => a + b, 0); // 所有玩家战力的总和
  const targetCount = 5; // 每组5个人
  const n = powers.length;

  let minDiff = Infinity; // 初始最小差设为无限大

  /**
   * DFS 函数
   * @param {number} index 当前搜索到的下标
   * @param {number} count 当前选中的人数
   * @param {number} sum 当前选中人数的战斗力和
   */
  function dfs(index, count, sum) {
    // 剪枝：人数超过5，非法
    if (count > targetCount) return;

    // 如果刚好选了5个人，计算当前分组与另一组的差值
    if (count === targetCount) {
      const otherSum = totalSum - sum; // 另一组的战斗力和
      const diff = Math.abs(sum - otherSum); // 两组差值
      minDiff = Math.min(minDiff, diff); // 更新最小差值
      return;
    }

    // 搜索完数组
    if (index === n) return;

    // 剪枝：如果当前状态已经不可能优于现有答案，就停止
    if (Math.abs(sum * 2 - totalSum) >= minDiff) return;

    // 选中当前玩家
    dfs(index + 1, count + 1, sum + powers[index]);

    // 不选当前玩家
    dfs(index + 1, count, sum);
  }

  dfs(0, 0, 0); // 从下标0开始，当前选中人数为0，战斗力和为0
  return minDiff;
}

```
时间复杂度：对于这个问题的输入大小（n = 10，k = 5），时间复杂度为 O(C(n, k)) = O(252)，可以视为 O(1)。  
空间复杂度：递归栈和路径存储的空间复杂度为 O(k) = O(5)，也可以视为 O(1) 对于固定 k。  


# 16、命令字符串加密
给定一个由多个命令字组成的命令字符串：  
1、字符串长度小于等于127字节，只包含大小写字母，数字，下划线和偶数个双引号；  
2、命令字之间以一个或多个下划线_进行分割；  
3、可以通过两个双引号””来标识包含下划线_的命令字或空命令字（仅包含两个双引号的命令字），双引号不会在命令字内部出现；  
请对指定索引的敏感字段进行加密，替换为******（6个*），并删除命令字前后多余的下划线_。  
如果无法找到指定索引的命令字，输出字符串ERROR。  
```JavaScript
/*
输入：2  aaa_password_"a12_45678"_timeout__100_""_    
输出：aaa_password_******_timeout_100_""  

*/
function processCommand(K, S) {
    const parts = [];
    let i = 0;
    const n = S.length;

    while (i < n) {
        if (S[i] === '"') {
            // 处理双引号内的命令字
            let start = i;
            i++; // 跳过第一个双引号
            while (i < n && S[i] !== '"') {
                i++;
            }
            i++; // 跳过第二个双引号
            parts.push(S.slice(start, i)); // 包括双引号本身
        } else {
            // 处理普通的命令字
            let start = i;
            while (i < n && S[i] !== '"' && S[i] !== '_') {
                i++;
            }
            parts.push(S.slice(start, i)); // 提取命令字并加入 parts
        }

        // 跳过一个或多个下划线
        while (i < n && S[i] === '_') {
            i++;
        }
    }

    // 检查索引是否有效
    if (K < 0 || K >= parts.length) {
        return "ERROR";
    }

    // 替换指定索引的命令字为 6 个星号
    parts[K] = "******";

    // 生成最终命令字符串
    let result = parts.join('_');

    // 删除首尾多余的下划线
    result = result.replace(/^_+|_+$/g, '');

    return result;
}


```
时间复杂度： O(n)，其中 n 是字符串的长度。  
空间复杂度： O(n)，其中 n 是字符串的长度。  


# 17、滑动窗口最大值（LeetCode 239）
给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。  
返回 滑动窗口中的最大值   

```JavaScript
function maxSlidingWindow(nums, k) {
  const deque = []; // 用来存索引，队列中的值对应 nums[索引] 是单调递减的
  const result = []; // 最终结果

  for (let i = 0; i < nums.length; i++) {
    // Step 1: 移除队尾所有比当前值小的元素，它们不可能是未来的最大值
    while (deque.length > 0 && nums[deque[deque.length - 1]] < nums[i]) {
      deque.pop(); // 弹出尾部较小值的索引
    }

    // Step 2: 把当前元素的索引加入队尾
    deque.push(i);

    // Step 3: 检查队首是否已经滑出窗口（不在当前窗口范围内）
    if (deque[0] <= i - k) {
      deque.shift(); // 移除已经过期的最大值索引
    }

    // Step 4: 只有形成了完整窗口后，才开始记录最大值
    if (i >= k - 1) {
      result.push(nums[deque[0]]); // 队首元素就是当前窗口最大值
    }
  }

  return result;
}

```
时间复杂度： O(n)，总共最多 2n 次队列操作，仍是线性时间。  
空间复杂度： O(k)，队列 deque 最多只会存放 当前窗口内的元素索引，即最多 k 个。



# 18、盛水最多的容器（LeetCode 11）
给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i]) 。
找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。
返回容器可以储存的最大水量。
说明：你不能倾斜容器。

```JavaScript
function maxArea(height) {
  let left = 0;                 // 左指针
  let right = height.length - 1; // 右指针
  let maxArea = 0;

  while (left < right) {
    const width = right - left;                         // 当前宽度
    const minHeight = Math.min(height[left], height[right]); // 当前高度
    const area = width * minHeight;                     // 当前面积
    maxArea = Math.max(maxArea, area);                  // 更新最大面积

    // 移动较短的一边
    if (height[left] < height[right]) {
      left++;
    } else {
      right--;
    }
  }

  return maxArea;
}


```
时间复杂度： O(n)，每个指针最多移动 n 次。    
空间复杂度： O(1)，只用了常量变量。  

# 19、接雨水（LeetCode 42）
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

```JavaScript
function trap(height) {
  let left = 0;                  // 左指针
  let right = height.length - 1; // 右指针
  let leftMax = 0;               // 左边历史最高柱子
  let rightMax = 0;              // 右边历史最高柱子
  let res = 0;                   // 最终接水量

  while (left < right) {
    // 不断更新左右最大高度
    leftMax = Math.max(leftMax, height[left]);
    rightMax = Math.max(rightMax, height[right]);

    // 决定由哪一边计算水量：较低的一侧才是“瓶颈”
    if (leftMax < rightMax) {
      // 左边较矮，可以确定水位
      res += leftMax - height[left]; // 当前柱子能接的水 = 水位 - 柱高
      left++;                        // 左指针向右移动
    } else {
      // 右边较矮，可以确定水位
      res += rightMax - height[right];
      right--;                       // 右指针向左移动
    }
  }

  return res;
}

}

```
时间复杂度： O(n)，每个指针最多移动 n 次。    
空间复杂度： O(1)，只用了常量变量。 

# 20、最大子数组和（LeetCode 53）
给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。  
子数组是数组中的一个连续部分。
```JavaScript
function maxSubArray(nums) {
  let maxSum = -Infinity; // 存储最终的最大子数组和
  let preSum = 0;         // 当前的前缀和（从头到当前位置的和）
  let minPreSum = 0;      // 当前遇到的最小前缀和

  for (let i = 0; i < nums.length; i++) {
    preSum += nums[i]; // 累加前缀和

    // 当前区间最大子数组和 = preSum - 最小前缀和
    // 意思是：从某个历史位置 minPreSum 到当前位置 i 之间的最大子数组和
    maxSum = Math.max(maxSum, preSum - minPreSum);

    // 更新最小前缀和：保留最小的前缀和位置，用于下一个位置比较
    minPreSum = Math.min(minPreSum, preSum);
  }

  return maxSum;
}


```
时间复杂度： O(n)  
空间复杂度： O(1)


# 21、合并区间（LeetCode 56）
以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回 一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间 。
```JavaScript
function merge(intervals) {
  if (intervals.length === 0) return [];

  // 第一步：按区间起点升序排序，确保遍历时先处理靠前的区间
  intervals.sort((a, b) => a[0] - b[0]);

  const result = []; // 用于保存合并后的区间

  // 遍历所有区间
  for (const interval of intervals) {
    const last = result.at(-1); // 获取当前结果中最后一个区间（注意：.at(-1) 是 ES2022+ 的新写法）

    if (!last || last[1] < interval[0]) {
      // 情况一：result 为空，或者当前区间和最后一个区间不重叠
      // 直接将当前区间加入结果数组
      result.push(interval);
    } else {
      // 情况二：有重叠，说明两个区间可以合并
      // 更新最后一个区间的结束位置为较大值
      last[1] = Math.max(last[1], interval[1]);
    }
  }

  return result; // 返回合并后的不重叠区间数组
}


```
时间复杂度： O(n log n)	排序占主导，n 为区间数  
空间复杂度： O(n)	最坏情况下结果仍保留所有区间	

# 22、轮转数组（LeetCode 189）
给定一个整数数组 nums，将数组中的元素向右轮转 k 个位置，其中 k 是非负数。
```JavaScript
function rotate(nums, k) {
  const n = nums.length;
  k %= n; // 防止 k > n 的情况

  // 反转函数，反转 nums[i..j]
  function reverse(i, j) {
    while (i < j) {
      [nums[i], nums[j]] = [nums[j], nums[i]];
      i++;
      j--;
    }
  }

  // 三步反转
  reverse(0, n - 1);     // 1. 整体反转
  reverse(0, k - 1);     // 2. 反转前 k 个
  reverse(k, n - 1);     // 3. 反转后 n-k 个
}

```
时间复杂度： O(n) 每个元素最多移动一次  
空间复杂度： O(1) 原地修改，无额外空间

# 23、除自身以外数组的乘积（LeetCode 238）
给你一个整数数组 nums，返回 数组 answer ，其中 answer[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积 。  
题目数据 保证 数组 nums之中任意元素的全部前缀元素和后缀的乘积都在  32 位 整数范围内。  
请不要使用除法，且在 O(n) 时间复杂度内完成此题。  
```JavaScript
function productExceptSelf(nums) {
  const n = nums.length;
  const answer = new Array(n).fill(1);

  // 第一次遍历：计算左边的乘积
  let left = 1;
  for (let i = 0; i < n; i++) {
    answer[i] = left;     // 存储左侧所有元素乘积
    left *= nums[i];      // 更新左乘积
  }

  // 第二次遍历：计算右边的乘积，同时乘到结果中
  let right = 1;
  for (let i = n - 1; i >= 0; i--) {
    answer[i] *= right;   // 乘上右侧所有元素乘积
    right *= nums[i];     // 更新右乘积
  }

  return answer;
}


```
时间复杂度：O(n)	两次线性遍历  
空间复杂度：O(1)	不算输出数组 answer，原地操作节省空间  

# 24、缺失的第一个正数（LeetCode 41）
给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。   
请你实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案。
```JavaScript
function firstMissingPositive(nums) {
    const n = nums.length;

    // 1. 处理数组中的无效值
    for (let i = 0; i < n; i++) {
        if (nums[i] <= 0 || nums[i] > n) {
            nums[i] = n + 1; // 将无效值设置为大于 n 的值
        }
    }

    // 2. 放每个元素到它应该出现的位置
    for (let i = 0; i < n; i++) {
        const num = Math.abs(nums[i]);
        if (num <= n && nums[num - 1] > 0) {
            nums[num - 1] = -Math.abs(nums[num - 1]);
        }
    }

    // 3. 查找第一个未标记的正整数
    for (let i = 0; i < n; i++) {
        if (nums[i] > 0) {
            return i + 1;
        }
    }

    // 如果都没有缺失，说明最小的正整数是 n + 1
    return n + 1;
}


```
时间复杂度： O(n)  
空间复杂度： O(1)




# 、（LeetCode ）

```JavaScript


```
时间复杂度： 
空间复杂度：




# 、（LeetCode ）

```JavaScript


```
时间复杂度： 
空间复杂度：

