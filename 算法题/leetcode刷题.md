# 一、两数之和

## 问

给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

## 示例

```javascript
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

## 答

1. 创建一个`map`
2. `for`循环遍历`nums`数组
3. 用`target减nums[i]`，以计算哪个数能跟当前的数字相加得到`target`
4. 检查map里有没有这个数，如果有则返回结果；如果没有则把`num[i]当作key，i当作value放入map中`，这样`以便于下次通过map.has()来检查map中有没有这个数，再通过map.get()来获取该元素原本的下标i`

```javascript
var twoSum = function(nums, target) {
  let map = new Map()
  let { length } = nums;
  for (let i = 0; i < length; i++) {
    let complement = target - nums[i]
    if (map.has(complement)) {
      return [map.get(complement) ,i]
    } else {
      map.set(nums[i], i)
    }
  }
};
```

# 二、两数相加

## 问

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

## 示例

```javascript
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

## 答

1. 创建一个`list`链表，同时定义一个新变量`temp`指向该链表，以便于后续获取链表头
2. `定义一个carry变量，初始值为零，用以保存进位`
3. 循环判断`l1和l2`不都为空，然后再判断`l1和l2`为空情况。若都不为空，则使得`current = l1.val + l2.val + carry`；若`l2为空，则使current= l1.val + carry`；若`l1为空，则使current= l2.val + carry`。计算结束之后，将`carry重新`置为零，若`current大于9，则将carry设为1，并且将current`取个位数数值。执行`temp.next = new ListNode(current)`，并将`temp、l1、l2`移动到下一节点
4. `while`循环结束之后，判断`carry`是否为1，若为1，则执行`temp.next = new ListNode(carry)`。
5. `最后return list.next`

```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */

var addTwoNumbers = function(l1, l2) {
  let list = new ListNode()
  let temp = list
  let carry = 0
  while(l1 || l2) {
    let current= 0
    if(l1 && l2) {
      current= l1.val + l2.val + carry
    } else if(l1 && !l2) {
      current= l1.val + carry
    } else if(!l1 && l2) {
      current= l2.val + carry
    }
    carry = 0
    if(current > 9) {
      carry = 1
      current= current % 10
    }
    temp.next = new ListNode(current)
    temp = temp.next
    l1 = l1 ? l1.next : l1
    l2 = l2 ? l2.next : l2
  }
  if(carry === 1) {
    temp.next = new ListNode(carry)
  }
  return list.next
};
```

# 三、无重复字符的最长子串

## 问

给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

## 示例

```javascript
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

## 答

`sliding window`（滑动窗口）

1. 创建一个`set`
2. 创建两个指针，第一个指针指向字符串的开头 `- j`，第二个指针随着`for`循环遍历字符串 `- i`
3. 声明变量`maxLength`用来存储最长子串的长度
4. 遍历字符串`s`，检查`set里是否有s[i]`
5. 如果`set`里没有`s[i]`，说明目前为止还没有重复的字符，把`s[i]`添加到`set`里，然后更新最大不重复字符的数量。
6. 如果`set里有s[i]`，则从`set里开始删除s[j]`，并且将`j`递增，再检查`set里是否有s[i]`，如此反复知道`set里没有s[i]`为止
7. 重复步骤`3和4`，直到遍历完整个字符串

```javascript
var lengthOfLongestSubstring = function(s) {
  let set = new Set()
  let i = 0, j = 0
  let maxLength = 0
  if (s.length === 0) {
    return 0
  }
  let { length } = s
  for (i; i < length; i++) {
    if (!set.has(s[i])) {
      set.add(s[i])
      maxLength = Math.max(maxLength, set.size)
    } else {
      while (set.has(s[i])) {
        set.delete(s[j])
        j++;
      }
      set.add(s[i])
    }
  }
  return maxLength
};
```

# 四、寻找两个正序数组的中位数

## 问

`给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。`

`请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。`

`你可以假设 nums1 和 nums2 不会同时为空。`

##  示例

```javascript
nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0


nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5
```

## 答

**解题思路：**创建一个`arr`数组，该数组只存储`nums1、nums2`中所有元素的一半，并且是从小到大排列。最后根据`nums1、nums2长度之和的奇偶性来返回arr`数组最后一个元素或者最后两个元素的平均数

```javascript
var findMedianSortedArrays = function(nums1, nums2) {
  let mid = nums1.length + nums2.length
  let left, right
  let arr = []
  for(let i = 0; i <= mid / 2; i++) {
    if(nums1.length === 0) {
      for(let j = i; j <= mid / 2; j++) {
        arr[j] = nums2.shift()
      }
      break
    } else if(nums2.length === 0) {
      for(let j = i; j <= mid / 2; j++) {
        arr[j] = nums1.shift()
      }
      break
    }
    arr[i] = nums1[0] < nums2[0] ? nums1.shift() : nums2.shift()
  }
  if(mid % 2 === 0) {
    return (arr[mid / 2 - 1] + arr[mid / 2]) / 2
  } else {
    return arr[Math.floor(mid / 2)]
  }
};
```

# 五、最长回文字符串

## 问

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

## 示例

```javascript
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

## 答

1. 如果字符串长度小于2，直接返回原字符串
2. 定义两个变量，一个`start`存储当前找到的最大回文字符串的起始位置，另一个`maxLength`记录字符串的长度（终止位置就是`start+maxLength`）
3. 创建一个`helper function`，判断左边和右边是否越界，同时最左边的字符是否等于最右边的字符。如果以上三个条件都满足，则根据当前回文长度和`maxLength`判断是否需要更新回文字符串最大长度及最大字符串的起始位置。然后将`left--，right++`，继续判断，直到不满足三个条件之一为止。
4. `遍历字符串，每个位置调用helper function两遍，第一遍检查i-1，i+1，第二遍检查i，i+1`

```javascript
var longestPalindrome = function(s) {
  let { length } = s
  if (length < 2) {
    return s
  }
  let start = 0, maxLength = 1
  function expandAroundCenter(left, right) {
    while(left >= 0 && right <= length && s[left] === s[right]) {
      if (right - left + 1 > maxLength) {
        start = left
        maxLength = right - left + 1
      }
      left--
      right++
    }
  }
  for (let i = 0; i < length - 1; i++) {
    expandAroundCenter(i - 1, i + 1)
    expandAroundCenter(i, i + 1)
  }
  return s.substring(start, start + maxLength)
};
```

# 六、盛最多水的容器

## 问

`给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。`

**说明：**你不能倾斜容器，且 n 的值至少为 2。

![](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

##  示例

```javascript
输入：[1,8,6,2,5,4,8,3,7]
输出：49
```

## 答

1. `定义一个max变量用来存储最大容量，left、right分别表示左右边界`
2. `执行while循环，当left>=right时退出，求出当前容量，实时更新max值。判断左右边界谁的值要大，当右边界大于左边界值时，则将left++；否则将right--`
3. 循环结束之后，`return max`

```javascript
var maxArea = function(height) {
  let max = 0
  let left = 0
  let right = height.length - 1
  while(left < right) {
    let area = Math.min(height[left], height[right]) * (right - left)
    max = Math.max(max, area)
    if(height[left] < height[right]) {
      let current = height[left]
      while(current > height[++left]) {}
    } else {
      let current = height[right]
      while(current > height[--right]) {}
    }
  }
  return max
};
```



# 七、三数之和

## 问

给你一个包含 `n` 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 `a，b，c` ，使得 `a + b + c = 0` ？请你找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

## 示例

```javascript
给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

## 答

1. 给数组排序
2. 遍历数组，从0遍历到`length - 2`（当i = length - 1和length - 2时，不符合条件）
3. 如果当前的数字等于前一个数字，则跳过这个数（避免出现重复三元组）
4. 如果数字不同，则设置`start = i + 1，end = length - 1`，查看`i、start和end三个数之和比零大还是小，如果比零小，start++，如果比零大，end--`，如果等于零，把这三个数加入到结果里。并将`start++，end--`，再判断`nums[start]与nums[start - 1]、nums[end]与nums[end + 1]`是否相等（避免出现重复三元组）
5. 返回结果

```javascript
var threeSum = function(nums) {
  let result = []
  let { length } = nums
  nums.sort((a, b) => {
    return a - b
  })
  for (let i = 0; i < length - 2; i++) {
    if (i === 0 || nums[i] !== nums[i - 1]) {
      let start = i + 1, end = length - 1
      while (start < end) {
        let sum = nums[i] + nums[start] + nums[end]
        if(sum === 0) {
          result.push([nums[i], nums[start], nums[end]])
          start++
          end--
          while(start < end && nums[start] === nums[start - 1]) {
            start++
          }
          while(start < end && nums[end] === nums[end + 1]) {
            end--
          }
        } else if(sum < 0) {
          start++
        } else {
          end--
        }
      }
    }
  }
  return result
};
```

# 八、电话号码的字母组合

## 问

给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

## 示例

```javascript
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

**说明:**

* 尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

## 答

1. 创建相应的映射表`mapping`
2. 循环遍历`digits`，每遍历一位便对`result和mapping[digits[i]]`进行组合即可
3. 遍历结束之后，`return result`

```javascript
var letterCombinations = function(digits) {
  let mapping = {
    2: ['a', 'b', 'c'],
    3: ['d', 'e', 'f'],
    4: ['g', 'h', 'i'],
    5: ['j', 'k', 'l'],
    6: ['m', 'n', 'o'],
    7: ['p', 'q', 'r', 's'],
    8: ['t', 'u', 'v'],
    9: ['w', 'x', 'y', 'z']
  }
  let result = []
  for(let i = 0; i < digits.length; i++) {
    let vari = []
    if(result.length === 0) {
      result = [...mapping[digits[i]]]
    } else {
      result.forEach(item => {
        mapping[digits[i]].forEach(str => {
          vari.push(item + str)
        })
      })
      result = vari
    }
  }
  return result
};
```



# 九、有效括号

## 问

给定一个只包括 `'('，')'，'{'，'}'，'['，']'` 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

## 示例

```javascript
输入: "{[]}"
输出: true

输入: "()[]{}"
输出: true

输入: "([)]"
输出: false
```

## 答

1. `创建一个HashMap，把括号配对放进去`
2. `创建一个stack（object），for循环遍历字符串，对于每一个字符，如果map里有这个key，那说明它是个左括号，从map里取出相对应的右括号，把它push到stack里。否则的话，它就是右括号，需要pop出stack里的第一个字符，然后看它是否等于当前的字符。如果不相符，则返回false。`
3. `循环结束后，如果stack不为空，说明还剩一些左括号没有被闭合，返回false。否则返回true`

```javascript
function isValid(s) {
  let obj = {
    '[': ']',
    '{': '}',
    '(': ')'
  }
  let stack = []
  let { length } = s
  for (let i = 0; i < length; i++) {
    let current = s[i]
    if (current in obj) {
      stack.push(obj[current])
    } else {
      if (current !== stack.pop()) {
        return false
      }
    }
  }
  return stack.length === 0
}
```

# 十、合并两个有序链表

## 问

将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

## 示例

```javascript
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

## 答

1. 创建一个`curr`链表，同时定义一个新变量`dummy`指向该链表，以便于后续获取链表头
2. 循环判断`l1和l2`不为空，然后再判断`l1.val和l2.val`的大小，当`l1.val小于l2.val`时，将`curr.next指向l1，然后再使l1链表进入到下一节点（类似于数组遍历中的i++）`，否则将`curr.next`指向`l2，然后再使l2链表进入到下一节点`。
3. 使`curr`链表进入下一节点
4. 循环结束之后，判断`l1或l2是否不为空，如果不为空则将curr.next指向l1或者l2`
5. 通过`dummy.next`获取新链表头部，并返回。（因为`dummy`当前节点为空节点，所以我们需要使用`next`来获取第一个真实节点）

```javascript
var mergeTwoLists = function(l1, l2) {
  let curr = new ListNode()
  let dummy = curr
  while(l1 !== null && l2 !== null) {
    if (l1.val < l2.val) {
      curr.next = l1
      l1 = l1.next
    } else {
      curr.next = l2
      l2 = l2.next
    }
    curr = curr.next
  }
  if (l1 !== null) {
    curr.next = l1
  }
  if (l2 != null) {
    curr.next = l2
  }
  return dummy.next
};
```

**链表**

# 十一、字母异位词分组

## 问

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

## 示例

```javascript
输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

**说明：**

- 所有输入均为小写字母。
- 不考虑答案输出的顺序。

## 答

1. 检查是否为空数组
2. 建立一个`HashMap`
3. 遍历字符串数组，并为每一个字符串都建立一个长度为26的数组，初始值为0。将字符串中字母的出现频率放到数组的对应位置里（利用`ASCII`码）
4. 按照相同字母出现频率作为`key`进行分组归类（使用`HashMap`）
5. 遍历`map`，将结果返回

```javascript
var groupAnagrams = function(strs) {
  if (strs.length === 0) {
    return []
  }
  const map = new Map()
  for (const str of strs) {
    const characters = new Array(26).fill(0)
    for (let i = 0; i < str.length; i++) {
      const ascii = str.charCodeAt(i) - 97
      characters[ascii]++;
    }
    const key = characters.join("")
    if (map.has(key)) {
      map.set(key, [...map.get(key), str])
    } else {
      map.set(key, [str])
    }
  }
  const result = []
  for(const arr of map) {
    result.push(arr[1])
  }
  return result
};
```

# 十二、最大子序和（动态规划）

## 问

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

## 示例

```javascript
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

## 答

1. 创建一个`memo`数组（用以保存每个下标处所存在的最大值，有可能是累加得到，也有可能是`nums`当前下标所对应的元素值，进行比较判断）
2. 将`memo的第一位数设置为nums`的第一位数（当前只有一位数，自然为最大值）
3. 定义`max`变量，用来存储最大子序之和
4. 遍历`nums`（从数组第二个元素开始），求取每一位的当前的较大子序之和，并存入`memo`数组中。再判断`memo`数组中当前下标对应的元素值与`max`值的大小，取大的值赋给`max`
5. 遍历结束之后，返回`max`结果

```javascript
var maxSubArray = function(nums) {
  let memo = []
  memo[0] = nums[0]
  let max = nums[0]
  let { length } = nums
  for (let i = 1; i < length; i++) {
    memo[i] = Math.max(nums[i], memo[i - 1] + nums[i])
    max = Math.max(memo[i], max)
  }
  return max
};
```

# 十三、螺旋矩阵

## 问

给定一个包含 *m* x *n* 个元素的矩阵（*m* 行, *n* 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。

## 示例

```javascript
输入:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
输出: [1,2,3,6,9,8,7,4,5]

输入:
[
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9,10,11,12]
]
输出: [1,2,3,4,8,12,11,10,9,5,6,7]
```

## 答

1. 如果数组为空，返回空数组
2. 定义4个边界以及当前方向
3. 当左边界小于等于右边界，且上边界小于等于下边界时，执行`while`循环：按照右、下、左、上的顺序，依次将路径上的字符添加到结果里
4. `while`循环结束后，返回结果

```javascript
var spiralOrder = function(matrix) {
  if (matrix.length === 0) {
    return []
  }
  let left = 0
  let right = matrix[0].length - 1
  let top = 0
  let bottom = matrix.length - 1

  let direction = 'right'
  let result = []
  while(left <= right && top <= bottom) {
    if (direction === 'right') {
      for (let i = left; i <= right; i++) {
        result.push(matrix[top][i])
      }
      top++
      direction = 'down'
    } else if (direction === 'down') {
      for (let i = top; i <= bottom; i++) {
        result.push(matrix[i][right])
      }
      right--
      direction = 'left'
    } else if (direction === 'left') {
      for (let i = right; i >= left; i--) {
        result.push(matrix[bottom][i])
      }
      bottom--
      direction = 'up'
    } else if (direction === 'up') {
      for (let i = bottom; i >= top; i--) {
        result.push(matrix[i][left])
      }
      left++
      direction = 'right'
    }
  }
  return result
};
```

# 十四、跳跃游戏

## 问

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

## 示例

```JavaScript
输入: [2,3,1,1,4]
输出: true
解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。
示例 2:

输入: [3,2,1,0,4]
输出: false
解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。
```

## 答案一：动态规划

![image-20200512171622502](http://img.lijiawei0627.xyz/img/image-20200512171622502.png)

### `top down`

**该方案性能较差**

1. 定义规则，0为状态未知，1为能够走通，-1为不能走通
2. 创建一个长度为`nums`长度的`memo`数组，并且初始值为0
3. 使`memo`数组的最后一个元素值为1
4. 定义`jump`函数，用以处理元素状态。判断`memo`当前元素为1还是-1，相应的返回`true或者false`。如果当前元素状态未知，则递归调用`jump`通过后面的元素状态来获取当前元素状态（模拟跳跃行为。注意边界，不可越过`nums`数组），以得到当前元素状态。
5. 返回`jump(0)`的状态

```javascript
var canJump = function(nums) {
  let { length } = nums
  let memo = new Array(length).fill(0)
  memo[length - 1] = 1
  function jump(position) {
    if (memo[position] === 1) {
      return true
    } else if (memo[position] === -1) {
      return false
    }
    let maxJump = Math.min(position + nums[position], length - 1)
    for (let i = position + 1; i <= maxJump; i++) {
      let jumpResutl = jump(i)
      if (jumpResutl === true) {
        memo[position] = 1
        return true
      }
    }
    memo[position] = -1
    return false
  }
  let result = jump(0)
  return result
}
```

### `bottom up`

**走楼梯问题也是`bottom up`**

1. 定义规则，1为能够走通，-1为不能走通
2. 创建一个长度为`nums长度的memo`数组，并且初始值为0
3. 使`memo`数组的最后一个元素值为1
4. 由后向前循环，根据当前已知状态的元素来判断未知元素状态
5. 判断`memo[0]状态，若为1则返回true，否则返回false`

```javascript
var canJump = function(nums) {
  let { length } = nums
  let memo = new Array(length).fill(0)
  memo[length - 1] = 1
  for (let i = length - 2; i >= 0; i--) {
    let maxJump = Math.min(i + nums[i], length - 1)
    for (let j = i + 1; j <= maxJump; j++) {
      if (memo[j] === 1) {
        memo[i] = 1
        break
      }
    } 
  }
  return memo[0] === 1
}
```

## 答案二：贪心算法

1. 创建一个变量`maxJump`，初始值为`length - 1`
2. 由后向前循环，判断当前元素下标与当前元素值之和是否大于`maxJump`
3. 若大于，则设置`maxJump`为当前元素下标与当前元素值之和
4. 循环遍历结束之后，若`maxJump为0，就返回true，否则返回false`

```javascript
var canJump = function(nums) {
  let { length } = nums
  let maxJump = length - 1
  for (let i = length - 2; i >= 0; i--) {
    if (nums[i] + i >= maxJump) {
      maxJump = i
    }
  }
  return maxJump === 0 ? true : false
}
```



# 十五、合并区间

## 问

给出一个区间的集合，请合并所有重叠的区间。

## 示例

```javascript
输入: [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].

输入: [[1,4],[4,5]]
输出: [[1,5]]
解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

## 答

1. 将数组中的区间按照起始位置排序
2. 创建`result`数组，用于存储合并区间之后的结果
3. 创建一个`curr数组，初始值为intervals[0]`
4. 用`curr`数组记录当前合并的最大区间，遍历数组中除第一个区间之外的其他所有区间，如果当前区间的起始位置小于等于`curr`的终点位置，则可以继续合并，所以合并并更新`curr`的起始和终止位置。如果当前区间得到起始位置大于`curr`的终止位置，则无法合并。所以将`curr加入到result`里。并用当前的区间替换`curr`的值。
5. 最后再将`curr数组添加到result`中一次（因为遍历结束之后，`curr数组肯定还有未被添加到result`中的区间）

```javascript
var merge = function(intervals) {
  if (intervals.length < 2) {
    return intervals
  }
  intervals.sort((a, b) => {
    return a[0] !== b[0] ? a[0] - b[0] : a[1] - b[1]
  })
  let result = []
  
  
  let current = intervals[0]
  intervals.splice(0, 1)
  for (let interval of intervals) {
    if (interval[0] <= current[1]) {
      current[1] = Math.max(current[1], interval[1])
    } else {
      result.push(current)
      current = interval
    }
  }
  result.push(current)
  return result
};
```

# 十六、不同路径

## 问

一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

问总共有多少条不同的路径？

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/robot_maze.png)

例如，上图是一个7 x 3 的网格。有多少可能的路径？

## 示例

```javascript
输入: m = 3, n = 2
输出: 3
解释:
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向右 -> 向下
2. 向右 -> 向下 -> 向右
3. 向下 -> 向右 -> 向右


输入: m = 7, n = 3
输出: 28
```

**提示：**

```javascript
1 <= m, n <= 100
题目数据保证答案小于等于 2 * 10 ^ 9
```

## 答

1. 创建一个`memo`数组，并将它初始化为长度为`n`，内部元素数组长度为`m`的二维数组
2. 使`memo[0]`中的所有元素值为1，`memo`中所有元素数组的第一个元素值为1
3. 遍历二维数组`memo`，使`memo[row][col] = memo[row - 1][col] + memo[row][col - 1]` （当前位置的条数等于左边元素的路径条数+上边元素的路径条数）
4. 返回`memo[n - 1][m - 1]`

```javascript
var uniquePaths = function(m, n) {
  let memo = []
  for (let i = 0; i < n; i++) {
    memo.push([])
    memo[i][0] = 1
  }
  for (let col = 0; col < m; col++) {
    memo[0][col] = 1
  }
  for (let row = 1; row < n; row++) {
    for (let col = 1; col < m; col++) {
      memo[row][col] = memo[row - 1][col] + memo[row][col - 1]
    }
  }
  return memo[n - 1][m - 1]
};
```

# 十七、加一

## 问

给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

## 示例

```javascript
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。



输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。
```

## 答

1. 存在两种情况，最后一位元素值为9或者小于9
2. 对最后一位元素进行加一操作
3. 若最后一位元素当前值大于9，则设置当前元素值为0，并向前进一。使用while进行循环操作
4. 否则直接返回

```javascript
var plusOne = function(digits) {
  let { length } = digits
  digits[length - 1]++
  let i = length - 1
  while(digits[i] > 9) {
    if (i === 0) {
      digits[i] = 0
      return [1, 0, ...digits]
    }
    digits[i] = 0
    digits[i - 1]++
    i--;
  }
  return digits
};
```

# 十八、爬楼梯

## 问

假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意：**给定 *n* 是一个正整数。

## 示例

```javascript
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶

输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```

## 答

**解题思想：**假设最后跨1阶，爬之前的台阶的方法为**n-1**阶楼梯的方法；如果跨2阶，那么爬之前的台阶就有**n-2**阶楼梯的方法。我们可以假设爬楼梯的方法函数为f()，台阶数为n，那么：**f(n)=f(n-1)+f(n-2)**;其实可以发现，随着楼梯数n的增加，爬法总数呈现斐波那契数列规律增加。那么我们是否可以设置爬一阶楼梯的方法为1，爬二阶楼梯的方法为2.这样当台阶数>2时，我们只需要**循环**相加就可以了。

```javascript
解法一：
var climbStairs = function(n) {
  const memo = [0, 1, 2]
  for(let i = 3; i <= n; i++) {
    memo[i] = memo[i - 1] + memo[i - 2]
  }
  return memo[n]
}


解法二：
var climbStairs = function(n) {
  if (n < 2) {
    return 1
  }
  var result, prev1 = 1, prev2 = 2
  for (var i = 2; i <= n; i++) {
    result = prev1 + prev2
    prev1 = prev2
    prev2 = result
  }
  return result
};
```

# 十九、矩阵置零

## 问

给定一个 `m x n` 的矩阵，如果一个元素为 0，则将其所在行和列的所有元素都设为 0。请使用**原地算法**。

> **原地算法：**在计算机科学中，一个原地算法（in-place algorithm）是一种使用小的，固定数量的额外之空间来转换资料的算法。当算法执行时，输入的资料通常会被要输出的部份覆盖掉。不是原地算法有时候称为非原地（not-in-place）或不得其所（out-of-place）。

## 示例

```javascript
输入: 
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
输出: 
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]


输入: 
[
  [0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]
]
输出: 
[
  [0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]
]
```

## 答

1. 检查并标记第一行和第一列是否有0（`firstRowHasZero和firstColHasZero`）
2. 使用第一行和第一列，来标记其余行列是否含有0
3. 接下来，利用第一行和第一列的标0情况，将`matrix`中的数字标0（从第二行第二列开始遍历矩阵元素，若当前元素所在的行或者列的第一个元素为0，那么就把当前元素值设为0）
4. 最后，处理第一行和第一列。`如果firstColHasZero等于true，将第一列全设为0；如果firstRowHasZero等于true，将第一行全设为0`

```javascript
var setZeroes = function(matrix) {
  let firstRowHasZero, firstColHasZero = false
  let { length } = matrix
  let len1 = matrix[0].length
  // 检查第一行是否有0
  if (matrix[0].indexOf(0) !== -1) {
    firstRowHasZero = true
  }
  // 检查第一列是否有0
  for (let i = 0; i < length; i++) {
    if (matrix[i][0] === 0) {
      firstColHasZero = true
      break;
    }
  }
  使用第一行和第一列标记其余行列是否含0
  for (let row = 1; row < length; row++) {
    for (let col = 1; col < len1; col++) {
      if (matrix[row][col] === 0) {
        matrix[row][0] = 0
        matrix[0][col] = 0
      }
    }
  }
  // 利用第一行和第一列的标0情况，将`matrix`中的数字标0
  for (let row = 1; row < length; row++) {
    for (let col = 1; col < len1; col++) {
      if (matrix[row][0] === 0 || matrix[0][col] === 0) {
        matrix[row][col] = 0
      }
    }
  }
	// 处理第一行第一列
  if (firstRowHasZero) {
    matrix[0] = Array(len1).fill(0)
  }
  if (firstColHasZero) {
    for (let i = 0; i < length; i++) {
      matrix[i][0] = 0
    }
  }
  return matrix
};
```

# 二十、生命游戏

## 问

生命游戏，简称为生命，是英国数学家约翰·何顿·康威在 1970 年发明的细胞自动机。

给定一个包含 m × n 个格子的面板，每一个格子都可以看成是一个细胞。每个细胞都具有一个初始状态：1 即为活细胞（live），或 0 即为死细胞（dead）。每个细胞与其八个相邻位置（水平，垂直，对角线）的细胞都遵循以下四条生存定律：

1. 如果活细胞周围八个位置的活细胞数少于两个，则该位置活细胞死亡；
2. 如果活细胞周围八个位置有两个或三个活细胞，则该位置活细胞仍然存活；
3. 如果活细胞周围八个位置有超过三个活细胞，则该位置活细胞死亡；
4. 如果死细胞周围正好有三个活细胞，则该位置死细胞复活；

根据当前状态，写一个函数来计算面板上所有细胞的下一个（一次更新后的）状态。下一个状态是通过将上述规则同时应用于当前状态下的每个细胞所形成的，其中细胞的出生和死亡是同时发生的。

## 示例

```javascript
输入： 
[
  [0,1,0],
  [0,0,1],
  [1,1,1],
  [0,0,0]
]
输出：
[
  [0,0,0],
  [1,0,1],
  [0,1,1],
  [0,1,0]
]
```

## 答

**解法一**

1. `创建一个board的副本copy`
2. 循环遍历二维数组`copy`的每一个元素
3. 创建一个临时变量`lives`用来记录该元素周围活细胞的数量
4. 根据`lives`数量和当前元素值，对`board[i][j]`状态进行更新
5. 遍历结束之后，返回`board`数组

```javascript
var gameOfLife = function(board) {
  let copy = JSON.parse(JSON.stringify(board))
  let rows = board.length
  let cols = board[0].length
  for (let i = 0; i < rows; i++) {
    for (let j = 0; j < cols; j++) {
      let lives = 0
      if (copy[i-1]) {
        if (copy[i-1][j] === 1) lives++ 
        if (copy[i-1][j-1] === 1) lives++
        if (copy[i-1][j+1]) lives++
      }
      if (copy[i][j-1]) lives++
      if (copy[i][j+1]) lives++
      if (copy[i+1]) {
        if (copy[i+1][j-1]) lives++
        if (copy[i+1][j]) lives++
        if (copy[i+1][j+1]) lives++
      }
      if (copy[i][j] === 1 && lives < 2) {
        board[i][j] = 0
      } else if (copy[i][j] === 1 && lives > 3) {
        board[i][j] = 0
      } else if (copy[i][j] === 0 && lives === 3) {
        board[i][j] = 1
      }
    }
  }
  return board
}
```

**解法二**

1. 循环遍历二维数组`board`的每一个元素
2. 创建一个临时变量`lives`用来记录该元素周围活细胞的数量
3. 根据`lives`数量和当前元素值，对`board[cow][col]`状态进行更新（由生变死记为 -1，由死变生记为2）
4. 更新之后，对`board`二维数组再次遍历，同时进行转换状态，2 -> 1，-1 -> 0
5. 遍历结束之后，返回`board`数组

```javascript
var gameOfLife = function (board) {
  if (!board.length) return board

  let rows = board.length;
  let cols = board[0].length;

  // 遍历每一个格子(细胞)，统计每个格子周围的存活细胞个数
  let neighbors = [0, -1, 1];
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      // 记录每个格子周围八个格子的存活情况
      let lives = 0;
      // 遍历每个格子的周围的八个格子
      for (let i = 0; i < 3; i++) {
        let r = row + neighbors[i];
        for (let j = 0; j < 3; j++) {
          if (!i && !j) continue
          let c = col + neighbors[j];
          // 注意：有些位置为 -1，但是当前它还是活的，这轮过后才死，所以当前我们仍然把它当做活的
          if ((r >= 0 && r < rows) && (c >= 0 && c < cols) && Math.abs(board[r][c]) === 1) {
            lives++;
          }
        }
      }

      // 通过 lives 判断当前格子是否存活
      // 由生变死记为 -1
      if ((board[row][col] === 1) && (lives < 2 || lives > 3)) {
        board[row][col] = -1;
      }
      // 由死变生记为2
      if (board[row][col] === 0 && lives === 3) {
        board[row][col] = 2;
      }
    }
  }

  // 转换状态，2 -> 1，-1 -> 0
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      board[row][col] = board[row][col] > 0 ? 1 : 0;
    }
  }
  return board
};
```

# 二十一、买卖股票的最佳时期

## 问

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。

注意：你不能在买入股票前卖出股票。

## 示例

```javascript
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
示例 2:

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

## 答

1. 创建一个`minPrice`变量，用来记录最小值
2. 创建一个`maxProfit`变量，用来记录最大利润
3. 遍历`prices`数组，实时记录最小值和最大利润
4. 返回`maxProfit`

```javascript
var maxProfit = function(prices) {
  let { length } = prices
  let minPrice = prices[0]
  let maxProfit = 0
  for (let i = 1; i < length; i++) {
    if (prices[i] < minPrice) {
      minPrice = prices[i]
    } else if (prices[i] - minPrice > maxProfit) {
      maxProfit = prices[i] - minPrice
    }
  }
  return maxProfit
};
```

# 二十二、验证回文串

## 问

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

**说明：**本题中，我们将空字符串定义为有效的回文串。

## 示例

```javascript
输入: "A man, a plan, a canal: Panama"
输出: true

输入: "race a car"
输出: false
```

## 答

1. 用正则表达式去除非数字和非字母
2. 如果字符串长度小于2，返回`true`
3. 定义两个指针，一个在字符串开头，一个在字符串结尾
4. 建立一个`while`循环，当`left<right`时执行循环，如果在任意地点`s[left]!==s[righrt],则return false`，否则`left++，right--`，继续执行循环。
5. 当循环完成后都没有`return false，则return true`

```javascript
var isPalindrome = function(s) {
  let str = s.toUpperCase().replace(/\W/g, '')
  let left = 0
  let right = str.length - 1
  while(left <= right) {
    if (str[left] !== str[right]) {
      return false
    }
    left++
    right--
  }
  return true
};
```

# 二十三、加油站

## 问

在一条环路上有 N 个加油站，其中第 i 个加油站有汽油 gas[i] 升。

你有一辆油箱容量无限的的汽车，从第 i 个加油站开往第 i+1 个加油站需要消耗汽油 cost[i] 升。你从其中的一个加油站出发，开始时油箱为空。

如果你可以绕环路行驶一周，则返回出发时加油站的编号，否则返回 -1。

**说明:** 

1. 如果题目有解，该答案即为唯一答案。
2. 输入数组均为非空数组，且长度相同。
3. 输入数组中的元素均为非负数。

## 示例

```javascript
输入: 
gas  = [1,2,3,4,5]
cost = [3,4,5,1,2]

输出: 3

解释:
从 3 号加油站(索引为 3 处)出发，可获得 4 升汽油。此时油箱有 = 0 + 4 = 4 升汽油
开往 4 号加油站，此时油箱有 4 - 1 + 5 = 8 升汽油
开往 0 号加油站，此时油箱有 8 - 2 + 1 = 7 升汽油
开往 1 号加油站，此时油箱有 7 - 3 + 2 = 6 升汽油
开往 2 号加油站，此时油箱有 6 - 4 + 3 = 5 升汽油
开往 3 号加油站，你需要消耗 5 升汽油，正好足够你返回到 3 号加油站。
因此，3 可为起始索引。



输入: 
gas  = [2,3,4]
cost = [3,4,3]

输出: -1

解释:
你不能从 0 号或 1 号加油站出发，因为没有足够的汽油可以让你行驶到下一个加油站。
我们从 2 号加油站出发，可以获得 4 升汽油。 此时油箱有 = 0 + 4 = 4 升汽油
开往 0 号加油站，此时油箱有 4 - 3 + 2 = 3 升汽油
开往 1 号加油站，此时油箱有 3 - 3 + 3 = 3 升汽油
你无法返回 2 号加油站，因为返程需要消耗 4 升汽油，但是你的油箱只有 3 升汽油。
因此，无论怎样，你都不可能绕环路行驶一周。
```



## 答

### 解法一：O($n^2$)

1. 创建一个`sum`变量用来存储绕环路行驶一周之后的所剩油量
2. 创建一个数组`result`用来记录当前下标对应的可加汽油与消耗汽油的差值
3. 遍历`gas、cost`数组，计算`sum`和记录`result`数组元素
4. `判断sum，若小于0则直接return -1`
5. 再次进行`for`循环，在循环中定义一个`count变量，初始值为result[i]`
6. `在for循环内部，从i + 1开始进行循环遍历，执行count += result操作`
7. `当count < 0（无法到达下一加油站）时退出内部循环，否则内部循环结束之后，判断count值，若大于等于0，则返回当前i`

```javascript
var canCompleteCircuit = function(gas, cost) {
  let { length } = gas
  let result = []
  let sum = 0
  for(let i = 0; i < length; i++) {
    sum = sum + (gas[i] - cost[i])
    result.push(gas[i] - cost[i])
  }
  if (sum < 0) {
    return -1
  }
  for (let i = 0; i < length; i++) {
    if (result[i] >= 0) {
      let count = result[i]
      let j = i === length - 1 ? 0 : i + 1
      for (j ; j !== i; j++) {
        if (count < 0) {
          break
        }
        count += result[j]
        if (j === length - 1) {
          j = -1
        }
      }
      if (count >= 0) {
        return i
      }
    }
  }
  return -1
};
```

### 解法二：O(n)

![image-20200518093856180](http://img.lijiawei0627.xyz/img/image-20200518093856180.png)

1. 创建一个`sum`变量用来存储绕环路行驶一周之后的所剩油量
2. 创建一个数组`result`用来记录当前下标对应的当前加油站可加汽油与行驶到下一个加油站所要消耗汽油的差值
3. 遍历`gas、cost`数组，计算`sum`和记录`result`数组元素
4. `判断sum，若小于0则直接return -1（无法跑完一周）`
5. `创建currentGas变量用来保存当前剩余油量，创建start变量用来存储出发时加油站编号`
6. `遍历result数组，currentGas += result[i]，若currentGas < 0，则设置start为i+1，currentGas=0`（`当currentGas小于0时，说明从start加油站出发无法跑完一周，而处于start与i之间的加油站同样不符合条件，因此从后一个加油站再次尝试`）
7. 遍历结束之后，返回`start`

```javascript
var canCompleteCircuit = function(gas, cost) {
  let { length } = gas
  let sum = 0
  let result = []
  for (let i = 0; i < length; i++) {
    sum += gas[i] - cost[i]
    result.push(gas[i] - cost[i])
  }
  if (sum < 0) {
    return -1
  }
  let currentGas = 0
  let start = 0
  for (let i = 0; i < length; i++) {
    currentGas += result[i]
    if (currentGas < 0) {
      start = i + 1
      currentGas = 0
    }
  }
  return start
}
```

# 二十四、乘积最大子数组

## 问

给你一个整数数组 `nums` ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

## 示例

```javascript
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。


输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```

## 答

解法与最大子序和一样，只是多了一个`minProductMemo`数组，用来记录最小值

1. 创建`maxProductMemo`数组和`minProductMemo数组`（分别用以保存每个下标处所存在的最大值和最小值，有可能是乘积，也有可能是`nums`当前下标所对应的元素值，进行比较判断）
2. 将`maxProductMemo和minProductMemo的第一位元素设置为nums`的第一位数（当前只有一位数，自然为最大值）
3. 定义`max`变量，用来存储最大乘积
4. 遍历`nums`（从数组第二个元素开始），求取每一位的当前的较大乘积和最小乘积，并分别存入`maxProductMemo、minProductMemo`数组中。再判断`maxProductMemo`数组中当前下标对应的元素值与`max`值的大小，取较大的值赋给`max`
5. 遍历结束之后，返回`max`结果

```javascript
var maxProduct = function(nums) {
  const maxProductMemo = []
  const minProductMemo = []
  maxProductMemo[0] = nums[0]
  minProductMemo[0] = nums[0]
  let max = nums[0]
  let { length } = nums
  for (let i = 1; i < length; i++) {
    let currentMax = maxProductMemo[i - 1] * nums[i]
    let currentMin = minProductMemo[i - 1] * nums[i]
    maxProductMemo[i] = Math.max(currentMax, nums[i], currentMin)
    minProductMemo[i] = Math.min(currentMax, nums[i], currentMin)
    max = Math.max(maxProductMemo[i], max)
  }
  return max
};
```

# 二十五、寻找旋转排序数组中的最小值

## 问

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

请找出其中最小的元素。

你可以假设数组中不存在重复元素。

## 示例

**示例 1:**

```javascript
输入: [3,4,5,1,2]
输出: 1
```

**示例 2:**

```javascript
输入: [4,5,6,7,0,1,2]
输出: 0
```

## 答

### 解法一

1. 如果数组长度为1，返回唯一的一个数
2. 定义两个指针，第一个`left`指向数组开头，第二个`right`指向数组结尾
3. 检查数组是否被反转，如果没有，则返回数组里的第一个数
4. 当`left`小于`right`时，取中间作为`mid`进行二分搜索，如果`mid`的左边一个数大于`mid`或者`mid`的右边一个数小于`mid`，则返回`mid或者mid + 1`
5. 如果`left所在的数小于mid，则将left右移至mid+1`位置（砍掉左半边）
6. 否则的话，将`right左移至mid-1`位置（砍掉右半边）

```javascript
var findMin = function(nums) {
  let { length } = nums
  if(length === 1 || nums[0] < nums[length - 1]) {
    return nums[0]
  }
  let left = 0
  let right = length - 1
  while(left < right) {
    let mid = Math.floor((left + right) / 2)
    if (nums[mid] > nums[mid + 1]) {
      return nums[mid + 1]
    }
    if (nums[mid - 1] > nums[mid]) {
      return nums[mid]
    }
    if (nums[left] < nums[mid]) {
      left = mid + 1
    } else {
      right = mid - 1
    }
  }
};
```

### 解法二

1. 如果数组长度为1，返回唯一的一个数
2. 检查数组是否被反转，如果没有，则返回数组里的第一个数
3. `遍历数组，当nums[i] < nums[i-1]时，return nuns[i]`

```javascript
var findMin = function(nums) {
  let { length } = nums
  if(length === 1 || nums[0] < nums[length - 1]) {
    return nums[0]
  }
  for (let i = length - 1; i > 0; i--) {
    if (nums[i] < nums[i - 1]) {
      return nums[i]
    }
  }
};
```

# 二十六、打家劫舍

## 问

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

## 示例

```javascript
输入: [1,2,3,1]
输出: 4
解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。



输入: [2,7,9,3,1]
输出: 12
解释: 偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

## 答

1. 创建一个`memo`数组，用来存储截止每一位元素能偷窃到的最高金额
2. `若nums长度小于等于1，则返回nums[0]或者0`
3. `使memo[0] = nums[0]，memo[1] = Math.max(nums[0], nums[1])（考虑[2,0,0,2]最高金额为4这一类情况）`
4. 从`nums`数组的第三项开始循环遍历，记录截止每一位元素能偷窃到的最高金额
5. 返回`memo[length - 1]`

```javascript
var rob = function(nums) {
  let memo = []
  let { length } = nums
  if (length <= 1) {
    return nums[0] ? nums[0] : 0
  }
  memo[0] = nums[0]
  memo[1] = Math.max(nums[0], nums[1])
  for (let i = 2; i < length; i++) {
    memo[i] = Math.max(memo[i - 2] + nums[i], memo[i - 1])
  }
  return memo[length - 1]
};


var rob = function(nums) {
  let memo = []
  let { length } = nums
  if (length <= 1) {
    return nums[0] ? nums[0] : 0
  }
  let prev1 = nums[0]
  let prev2 = Math.max(nums[0], nums[1])
  for (let i = 2; i < length; i++) {
    const temp = Math.max(nums[i] + prev1, prev2)
    prev1 = prev2
    prev2 = temp
  }
  return prev2
};
```

# 二十七、岛屿数量

## 问

给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

##  示例

```javascript
输入:
11110
11010
11000
00000
输出: 1


输入:
11000
11000
00100
00011
输出: 3
解释: 每座岛屿只能由水平和/或竖直方向上相邻的陆地连接而成。
```

## 答

**解题思想：**遍历二维数组，当遇到元素值为1的元素时，对`count++`，再以该元素为中心，向上下左右四个方向递归搜索。将相邻元素值为1的元素设置元素值为0。遍历结束之后，`return count`

```javascript
var numIslands = function(grid) {
  let { length } = grid
  if (length === 0) {
    return 0
  }
  let len1 = grid[0].length
  let count = 0
  function dfs (row, col) {
    if (row < 0 || row >= length || col < 0 || col >= len1 || grid[row][col] === '0') {
      return
    }
    grid[row][col] = '0'
    dfs(row + 1, col)
    dfs(row - 1, col)
    dfs(row, col + 1)
    dfs(row, col - 1)
  }
  for (let row = 0; row < length; row++) {
    for (let col = 0; col < len1; col++) {
      if (grid[row][col] === '1') {
        count++
        dfs(row, col)
      }
    }
  }
  return count
};
```

# 二十八、存在重复元素

## 问

给定一个整数数组，判断是否存在重复元素。

如果任意一值在数组中出现至少两次，函数返回 `true` 。如果数组中每个元素都不相同，则返回 `false` 。

## 示例

```javascript
输入: [1,2,3,1]
输出: true


输入: [1,2,3,4]
输出: false


输入: [1,1,1,3,3,4,3,2,4,2]
输出: true
```

## 答

**解题思想：**`先给nums数组排序，然后定义一个ahead变量，初始值为nuns[0]。遍历nums数组，当存在两个相邻元素值相等时返回true。否则遍历结束之后返回false`

```javascript
var containsDuplicate = function(nums) {
  nums.sort((a, b) => {
    return a - b
  })
  let ahead = nums[0]
  let { length } = nums
  for (let i = 1; i < length; i++) {
    if (ahead === nums[i]) {
      return true
    }
    ahead = nums[i]
  }
  return false
};
```

# 二十九、存在重复元素`II`

## 问

`给定一个整数数组和一个整数 k，判断数组中是否存在两个不同的索引 i 和 j，使得 nums [i] = nums [j]，并且 i 和 j 的差的 绝对值 至多为 k。`

## 示例

```javascript
输入: nums = [1,2,3,1], k = 3
输出: true


输入: nums = [1,0,1,1], k = 1
输出: true


输入: nums = [1,2,3,1,2,3], k = 2
输出: false
```

## 答

1. 创建一个`map`
2. `遍历nums数组，判断map中是否存在nums[i]，存在时，判断i - map.get(nums[i])是否小于等于k，若满足以上两个条件，则return true，否则执行map.set(nums[i], i)`
3. 遍历结束之后，`return false`

```javascript
var containsNearbyDuplicate = function(nums, k) {
  const map = new Map()
  let { length } = nums
  for (let i = 0; i < length; i++) {
    if (map.has(nums[i]) && i - map.get(nums[i]) <= k) {
      return true
    }
    map.set(nums[i], i)
  }
  return false
}
```

# 三十、除自身以外数组的乘积

## 问

`给你一个长度为 n 的整数数组 nums，其中 n > 1，返回输出数组 output ，其中 output[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积。`

##  示例

```javascript
输入: [1,2,3,4]
输出: [24,12,8,6]
```

**提示：**

* 题目数据保证数组之中任意元素的全部前缀元素和后缀（甚至是整个数组）的乘积都在 32 位整数范围内。

说明: 

* 请不要使用除法，且在 `O(n)` 时间复杂度内完成此题。

进阶：

* 你可以在常数空间复杂度内完成这个题目吗？（ 出于对空间复杂度分析的目的，输出数组不被视为额外空间。）

## 答

1. `创建一个长度为nums.length的数组，数组中元素值均为1`
2. `定义一个初始值为1的变量product`
3. 从左往右遍历`nums`数组，使`result`数组中的元素值为对应`nums`数组中当前下标左边的所有元素乘积。
4. 遍历结束之后，给变量`product`赋值为1
5. 再次遍历`nums`数组，由于`result`数组中已经存储了当前下标左边的所有元素乘积，我们只需要用`result`中的元素值去乘以当前下标右边的所有元素乘积就可以得到除当前元素之外其余各元素的乘积
6. 遍历结束之后，返回`result`

```javascript
var productExceptSelf = function(nums) {
  let { length } = nums
  let result = new Array(length).fill(1)
  let product = 1
  for (let i = 0; i < length; i++) {
    result[i] = result[i] * product
    product = product * nums[i]
  }
  product = 1
  for (let i = length - 1; i >= 0; i--) {
    result[i] = result[i] * product
    product = product * nums[i]
  }
  return result
}
```

# 二十一、有效的字母异位词

## 问

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

## 示例

```javascript
输入: s = "anagram", t = "nagaram"
输出: true


输入: s = "rat", t = "car"
输出: false
说明:
你可以假设字符串只包含小写字母。
```

**进阶:**

* 如果输入字符串包含 `unicode` 字符怎么办？你能否调整你的解法来应对这种情况？

## 答

### 解法一

1. 如果两个数组长度不一致，则返回`false`
2. 创建一个map，用来存储每个字符出现的次数
3. `对于第一个单词的每个字母，也就是s1[i]，在map里将出现次数+1.对于第二个单词的每个字母，也就是s2[i]，在map里将出现次数-1`
4. `遍历完成后，检查map里的每一个字母出现次数是不是为0，如果有一个非0的字母，则返回false，否则返回true`

```javascript
var isAnagram = function(s, t) {
  if (s.length !== t.length) {
    return false
  }
  let map = new Map()
  for (let i = 0; i < s.length; i++) {
    if (map.has(s[i])) {
      map.set(s[i], map.get(s[i]) + 1)
    } else {
      map.set(s[i], 1)
    }

    if (map.has(t[i])) {
      map.set(t[i], map.get(t[i]) - 1)
    } else {
      map.set(t[i], -1)
    }
  }
  for (const item of map) {
    if (item[1] !== 0) {
      return false
    }
  }
  return true
}
```

### 解法二

1. 创建一个长度为26的数组，初始值为0。
2. 遍历字符串`s`，将字符串中字母的出现频率放到数组的对应位置里（利用`ASCII`码）
3. 再遍历字符串`t`，将字符串中字母的出现频率放到数组的对应位置里（利用`ASCII`码）
4. 最后将两个数组转化成字符串，判断两个字符串是否相等。`若相等则返回true，否则返回false`

```javascript
var isAnagram = function(s, t) {
  let characters = new Array(26).fill(0)
  for (let i = 0; i < s.length; i++) {
    const ascii = s.charCodeAt(i) - 97
    characters[ascii]++;
  }
  let s1 = characters.join('')
  characters = new Array(26).fill(0)
  for (let i = 0; i < t.length; i++) {
    const ascii = t.charCodeAt(i) - 97
    characters[ascii]++;
  }
  let t1 = characters.join('')
  if (t1 === s1) {
    return true
  }
  return false
}
```

# 三十二、移动零

## 问

给定一个数组 `nums`，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

## 示例

```javascript
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

**说明:**

* 必须在原数组上操作，不能拷贝额外的数组。
* 尽量减少操作次数。

## 答

1. 遍历`nums`数组，将数组中所有非零元素按顺序提到数组前面。
2. 再将数组的剩余元素补充为0

```javascript
var moveZeroes = function(nums) {
  let j = 0
  for (let i = 0; i < nums.length; i++) {
    if (nums[i] !== 0) {
      nums[j] = nums[i]
      j++
    }
  }
  for (let i = j; i < nums.length; i++) {
    nums[i] = 0
  }
  return nums
}
```

# 三十三、两个数组的交集

## 问

给定两个数组，编写一个函数来计算它们的交集。

## 示例

```javascript
输入: nums1 = [1,2,2,1], nums2 = [2,2]
输出: [2]


输入: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出: [9,4]
```

**说明:**

* 输出结果中的每个元素一定是唯一的。
* 我们可以不考虑输出结果的顺序。

## 答

1. 创建一个`result set`
2. `将nums2转换成set类型`
3. `遍历nums1，同时判断set中是否包含遍历元素，如果包含的话，执行result.add(num)`
4. `遍历结束之后，return [...result]`

```javascript
var intersection = function(nums1, nums2) {
  let result = new Set()
  let set = new Set(nums2)
  for (let num of nums1) {
    if (set.has(num)) {
      result.add(num)
    }
  }
  return [...result]
}
```

# 三十四、甲板上的战舰

## 问

给定一个二维的甲板， 请计算其中有多少艘战舰。 战舰用 'X'表示，空位用 '.'表示。 你需要遵守以下规则：

* 给你一个有效的甲板，仅由战舰或者空位组成。
* 战舰只能水平或者垂直放置。换句话说,战舰只能由 `1xN (1 行, N 列)组成，或者 Nx1 (N 行, 1 列)组成，其中N可以是任意大小。`
* 两艘战舰之间至少有一个水平或垂直的空位分隔 - 即没有相邻的战舰。

## 示例

```javascript
X..X
...X
...X
```

在上面的甲板中有2艘战舰。

## 无效样例 

```javascript
...X
XXXX
...X
```

你不会收到这样的无效甲板 - 因为战舰之间至少会有一个空位将它们分开。

**进阶:**

* 你可以用一次扫描算法，只使用O(1)额外空间，并且不修改甲板的值来解决这个问题吗？

## 答

### 解法一

**解题思想：**遍历二维数组，当遇到元素值为`'X'`的元素时，对`count++`，再以该元素为中心，向上下左右四个方向递归搜索。将相邻元素值为`'X'`的元素设置元素值为'.'。最后返回`count`

```javascript
var countBattleships = function(board) {
  if (board.length === 0) {
    return 0
  }
  function dfs(row, col) {
    if (row < 0 || row >= board.length || col < 0 || col >= board[0].length || board[row][col] !== 'X') {
      return
    }
    board[row][col] = '.'
    dfs(row - 1, col)
    dfs(row + 1, col)
    dfs(row, col - 1)
    dfs(row, col + 1)
  }
  let count = 0
  for (let row = 0; row < board.length; row++) {
    for (let col = 0; col < board[0].length; col++){
      if (board[row][col] === 'X') {
        count++
        dfs(row, col)
      }
    }
  }
  return count
};
```

### 解法二

1. 遍历二维数组`board`
2. 由于战舰只能水平或者垂直放置，所以我们只当遍历到战舰的开头位置时才进行`count++`，这样可以正确获取战舰数量
3. 遍历结束之后，`return count`

```javascript
var countBattleships = function(board) {
  if (board.length === 0) {
    return 0
  }
  let count = 0
  for (let row = 0; row < board.length; row++) {
    for (let col = 0; col < board[0].length; col++) {
      if (board[row][col] === 'X' && (row === 0 || board[row - 1][col] !== 'X') && (col === 0 || board[row][col - 1] !== 'X')) {
        count++
      }
    }
  }
  return count
}
```

# 三十五、斐波那契数

## 问

斐波那契数，通常用 F(n) 表示，形成的序列称为斐波那契数列。该数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：

```javascript
F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
```

给定 N，计算 F(N)。

##  示例

```javascript
输入：2
输出：1
解释：F(2) = F(1) + F(0) = 1 + 0 = 1.


输入：3
输出：2
解释：F(3) = F(2) + F(1) = 1 + 1 = 2.


输入：4
输出：3
解释：F(4) = F(3) + F(2) = 2 + 1 = 3.
```

**提示：**

* 0 ≤ N ≤ 30

## 答

1. 创建一个`memo`数组，用以存储每一项斐波那契数，并使`memo[0]=0,memo[1]=1`
2. 执行for循环，使`memo[i] = memo[i - 1] + memo[i - 2]`
3. 循环结束之后，`return memo[N]`

```javascript
var fib = function(N) {
  let memo = []
  memo[0] = 0
  memo[1] = 1
  for (let i = 2; i <= N; i++) {
    memo[i] = memo[i - 1] + memo[i - 2]
  }
  return memo[N]
}
```

# 三十六、子数组最大平均数I

## 问

给定 n 个整数，找出平均数最大且长度为 k 的连续子数组，并输出该最大平均数。

## 示例

```javascript
输入: [1,12,-5,-6,50,3], k = 4
输出: 12.75
解释: 最大平均数 (12-5-6+50)/4 = 51/4 = 12.75
```

**注意:**

* 1 <= k <= n <= 30,000。
* 所给数据范围 [-10,000，10,000]。

## 答

1. 创建一个`sum`变量，初始值为数组中前`k`个元素值之和
2. `创建一个maxAverage变量，初始值为sum / k`，用以存储子数组最大平均数
3. 遍历`nums`数组，通过不断的执行`sum - nums[i - k] + nums[i]`，来获取最大平均数
4. 遍历结束之后，`return maxAverage`

```javascript
var findMaxAverage = function(nums, k) {
  let sum = 0
  for (let i = 0; i < k; i++) {
    sum += nums[i]
  }
  let maxAverage = sum / k
  for (let i = k; i < nums.length; i++) {
    sum = sum - nums[i - k] + nums[i]
    let currentMax = sum / k
    if (currentMax > maxAverage) {
      maxAverage = currentMax
    }
  }
  return maxAverage
};
```

# 三十七、有效三角形个数

## 问

给定一个包含非负整数的数组，你的任务是统计其中可以组成三角形三条边的三元组个数。

## 示例

```javascript
输入: [2,2,3,4]
输出: 3
解释:
有效的组合是: 
2,3,4 (使用第一个 2)
2,3,4 (使用第二个 2)
2,2,3
```

**注意:**

* 数组长度不超过1000。
* 数组里整数的范围为 [0, 1000]。

## 答

**说明：要想构成三角形，只需三角形中两条最短边之和大于最长边即可**

1. 先给`nums`数组进行从小到大排序
2. `创建count变量，用以存储有效三角形个数，初始值为0`
3. 遍历`nums`数组，为每次遍历都创建一个`left、right变量，初始值分别为i + 1、i + 2`。然后再在内部循环遍历，直到`right大于等于nums.length`时退出。内部循环判断：当`nums[i]+nums[left]大于nums[right]`时，`count++，并且right++（注意：当right === nums.length - 1时，应该执行left++，right=left+1）`；`否则left++，并且使right = left + 1`

```javascript
var triangleNumber = function(nums) {
  nums.sort((a, b) => {
    return a - b
  })
  let count = 0
  for (let i = 0; i < nums.length - 2; i++) {
    let left = i + 1
    let right = i + 2
    while(right < nums.length) {
      let sum = nums[i] + nums[left]
      if (sum > nums[right]) {
        count++
        if (right === nums.length - 1) {
          left++
          right = left + 1
        } else {
          right++
        }
      } else {
        left++
        right = left + 1
      }
    }
  }
  return count
};
```

# 三十八、验证回文字符串II

## 问

给定一个非空字符串 s，最多删除一个字符。判断是否能成为回文字符串。

## 示例

```javascript
输入: "aba"
输出: True


输入: "abca"
输出: True
解释: 你可以删除c字符。
```

**注意:**

* 字符串只包含从 a-z 的小写字母。字符串的最大长度是50000。

## 答

1. `定义left、right指针，初始值分别为0、s.length-1`
2. `使用left、right指针遍历字符串，当出现s[left] !== s[right]时，分别删除left、right指针所在元素，看剩余字符串是否能够构成回文字符串。若其中任意一种情况可以构成回文字符串，则return true`
3. 若遍历结束，也未出现`s[left] !== s[right]`，则直接`return true`

```javascript
var validPalindrome = function(s) {
  function isPalindrome(left, right) {
    while(left <= right) {
      if (s[left] !== s[right]) {
        return false
      }
      left++
      right--
    }
    return true
  }
  let left = 0
  let right = s.length - 1
  while(left <= right) {
    if (s[left] !== s[right]) {
      return isPalindrome(left+1, right) || isPalindrome(left, right-1)
    }
    left++
    right--
  }
  return true
}
```

# 三十九、岛屿的最大面积

## 问

给定一个包含了一些 0 和 1 的非空二维数组 grid 。

一个 岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在水平或者竖直方向上相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着。

找到给定的二维数组中最大的岛屿面积。(如果没有岛屿，则返回面积为 0 。)

## 示例 

```javascript
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
对于上面这个给定矩阵应返回 6。注意答案不应该是 11 ，因为岛屿只能包含水平或垂直的四个方向的 1 。


[[0,0,0,0,0,0,0,0]]
对于上面这个给定的矩阵, 返回 0。
```

**注意:**

* 给定的矩阵grid 的长度和宽度都不超过 50。

## 答

**解题思想：**遍历二维数组，当遇到元素值为1的元素时，执行`dfs(row, col)`。在`dfs`函数中，每次遇到符合条件的元素都进行`count++`，用于统计小岛面积，并将该元素值为1的元素设置元素值为0。再以该元素为中心，使用`dfs`函数进行向上下左右四个方向递归搜索。每次统计完小岛面积之后，都对`maxArea`进行更新。最终二维数组遍历结束之后，`return maxArea`

```javascript
var maxAreaOfIsland = function(grid) {
  function dfs (row, col) {
    if (row < 0 || row >= length || col < 0 || col >= len1 || grid[row][col] === 0) {
      return
    }
    count++
    grid[row][col] = 0
    dfs(row + 1, col)
    dfs(row - 1, col)
    dfs(row, col + 1)
    dfs(row, col - 1)
  }
  let length = grid.length
  let len1 = grid[0].length
  let maxArea = 0
  let count = 0
  for (let row = 0; row < length; row++) {
    for (let col = 0; col < len1; col++) {
      if(grid[row][col] === 1) {
        dfs(row, col)
        maxArea = Math.max(maxArea, count)
        count = 0
      }
    }
  }
  return maxArea
};
```

# 四十、二分查找

## 问

给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`  ，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 -1。

## 示例

```javascript
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4


输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

**提示：**

* `你可以假设 nums 中的所有元素是不重复的。`
* `n 将在 [1, 10000]之间。`
* `nums 的每个元素都将在 [-9999, 9999]之间。`

## 答

1. 定义`left=0，right=length-1，mid=Math.floor(left + right)/2`
2. `当left<=right，如果mid上的值等于target，返回mid，如果小于target，left=mid+1（舍掉左半边）`；如果大于`target，right=mid+1`（舍掉右半边）
3. 如果`while`循环结束后都没有找到`target`，返回`-1`

```javascript
var search = function(nums, target) {
  let left = 0
  let right = nums.length - 1
  while(left <= right) {
    let mid = Math.floor((left + right) / 2)
    if (nums[mid] === target) {
      return mid
    } else if(nums[mid] < target) {
      left = mid + 1
    } else if(nums[mid] > target) {
      right = mid - 1
    }
  }
  return -1
};
```

# 四十一、图像渲染

## 问

有一幅以二维整数数组表示的图画，每一个整数表示该图画的像素值大小，数值在 0 到 65535 之间。

给你一个坐标 `(sr, sc)` 表示图像渲染开始的像素值（行 ，列）和一个新的颜色值 `newColor`，让你重新上色这幅图像。

为了完成上色工作，从初始坐标开始，记录初始坐标的上下左右四个方向上像素值与初始坐标相同的相连像素点，接着再记录这四个方向上符合条件的像素点与他们对应四个方向上像素值与初始坐标相同的相连像素点，……，重复该过程。将所有有记录的像素点的颜色值改为新的颜色值。

最后返回经过上色渲染后的图像。

## 示例

```javascript
输入: 
image = [[1,1,1],[1,1,0],[1,0,1]]
sr = 1, sc = 1, newColor = 2
输出: [[2,2,2],[2,2,0],[2,0,1]]
解析: 
在图像的正中间，(坐标(sr,sc)=(1,1)),
在路径上所有符合条件的像素点的颜色都被更改成2。
注意，右下角的像素没有更改为2，
因为它不是在上下左右四个方向上与初始点相连的像素点。
```

**注意:**

* `image 和 image[0] 的长度在范围 [1, 50] 内。`
* 给出的初始点将满足 `0 <= sr < image.length 和 0 <= sc < image[0].length。`
* `image[i][j] 和 newColor` 表示的颜色值在范围 [0, 65535]内。

## 答

**解题思想：**当`image[sr][sc] === newColor时，直接return image`。定义一个`dfs`函数，用以改变像素值，和向上下左右四个方向递归搜索。调用`dfs`函数`dfs(sr, sc)`，最后`return image`

```javascript
var floodFill = function(image, sr, sc, newColor) {
  if(image[sr][sc] === newColor) {
    return image
  }
  let oldColor = image[sr][sc]
  function dfs(row, col) {
    if (row < 0 || row >= image.length || col < 0 || col >= image[0].length || image[row][col] !== oldColor) {
      return
    }
    image[row][col] = newColor
    dfs(row + 1, col)
    dfs(row - 1, col)
    dfs(row, col - 1)
    dfs(row, col + 1)
  }
  dfs(sr, sc)
  return image
};
```

# 四十二、旋转字符串

## 问

给定两个字符串, A 和 B。

A 的旋转操作就是将 A 最左边的字符移动到最右边。 例如, 若 `A = 'abcde'`，在移动一次之后结果就是`'bcdea'` 。如果在若干次旋转操作之后，A 能变成B，那么返回True。

## 示例

```javascript
输入: A = 'abcde', B = 'cdeab'
输出: true


输入: A = 'abcde', B = 'abced'
输出: false
```

**注意：**

* A 和 B 长度不超过 100。

## 答

1. 判断A是否等于B，若等于则直接`return true`
2. `判断A.length是否等于B.length，若不等于则直接return false`
3. 循环遍历A，不断的将A最左边的字符移动到最右边，然后判断A是否等于B，若等于则`return true`
4. 遍历结束后，直接`return false`

```javascript
var rotateString = function(A, B) {
  if(A === B) {
    return true
  }
  if(A.length !== B.length) {
    return false
  }
  for(let i = 0; i < A.length; i++) {
    A = A.slice(1) + A[0]
    if(A === B) {
      return true
    }
  }
  return false
};


var rotateString = function(A, B) {
  if(A.length !== B.length) {
    return false
  }
  let str = A + A
  return str.includes(B)
}
```

# 四十三、矩形重叠

## 问

矩形以列表 `[x1, y1, x2, y2]` 的形式表示，其中 `(x1, y1)` 为左下角的坐标，`(x2, y2)` 是右上角的坐标。

如果相交的面积为正，则称两矩形重叠。需要明确的是，只在角或边接触的两个矩形不构成重叠。

给出两个矩形，判断它们是否重叠并返回结果。

## 示例

```javascript
输入：rec1 = [0,0,2,2], rec2 = [1,1,3,3]
输出：true


输入：rec1 = [0,0,1,1], rec2 = [1,0,2,1]
输出：false
```

**提示：**

* 两个矩形 rec1 和 rec2 都以含有四个整数的列表的形式给出。
* 矩形中的所有坐标都处于 -10^9 和 10^9 之间。
* x 轴默认指向右，y 轴默认指向上。
* 你可以仅考虑矩形是正放的情况。

## 答

**主要是注意两个矩形的边界**

![image-20200528111238803](http://img.lijiawei0627.xyz/img/image-20200528111238803.png)

```javascript
var isRectangleOverlap = function(rec1, rec2) {
  if(rec1[2] <= rec2[0] || rec1[0] >= rec2[2] || rec1[1] >= rec2[3] || rec1[3] <= rec2[1]) {
    return false
  }
  return true
};
```

# 四十四、单词拆分

## 问

给定一个非空字符串 s 和一个包含非空单词列表的字典 `wordDict`，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

* 拆分时可以重复使用字典中的单词。
* 你可以假设字典中没有重复的单词。

## 示例

```javascript
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。



输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。



输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

## 答

1. `创建一个长度为s.length的memo数组，并且将初始值置为false。memo[i]表示0-i之间的字符串是否可以被拆分并满足题设条件存在于wordDict中`
2. `根据wordDict生成set`
3. `遍历s，先判断set中是否包含s.slice(0, i + 1)，若包含则将memo[i]设为true`，否则再在内部进行遍历，当`memo[j]和set.has(s.slice(j + 1, i + 1))都为true时，将memo[i]设为true（0<=j<i）`
4. 遍历结束之后，`return memo[s.length - 1]`

```javascript
var wordBreak = function(s, wordDict) {
  let memo = new Array(s.length).fill(false)
  let set = new Set(wordDict)
  for(let i = 0; i < s.length; i++) {
    if(set.has(s.slice(0, i + 1))) {
      memo[i] = true
      continue
    }
    for(let j = 0; j < i; j++) {
      if(memo[j] && set.has(s.slice(j + 1, i + 1))) {
        memo[i] = true
        break
      }
    }
  }
  return memo[s.length - 1]
};
```

# 四十五、比较含退格的字符串

## 问

给定 S 和 T 两个字符串，当它们分别被输入到空白的文本编辑器后，判断二者是否相等，并返回结果。 `#` 代表退格字符。

**注意：如果对空文本输入退格字符，文本继续为空。**

##  示例

```javascript
输入：S = "ab#c", T = "ad#c"
输出：true
解释：S 和 T 都会变成 “ac”。


输入：S = "ab##", T = "c#d#"
输出：true
解释：S 和 T 都会变成 “”。


输入：S = "a##c", T = "#a#c"
输出：true
解释：S 和 T 都会变成 “c”。


输入：S = "a#c", T = "b"
输出：false
解释：S 会变成 “c”，但 T 仍然是 “b”。
```

**提示：**

* `1 <= S.length <= 200`
* `1 <= T.length <= 200`
* `S 和 T 只含有小写字母以及字符 '#'。`

**进阶：**

* 你可以用 `O(N)` 的时间复杂度和 `O(1)` 的空间复杂度解决该问题吗？
  1. `定义i、j两个指针，分别指向S、T的末尾`
  2. `定义backspaceS、backspaceT两个变量，初始值为0，用以实时记录退格数。`
  3. `执行while循环，在内部分别对S、T字符串从后往前进行遍历，当遇到"#"时，对backspace进行加一，同时将指针向前进一位，当遇到非"#"，且backspace大于零时，则将指针直接向前进一位。若以上条件都不满足时，退出循环`（模拟退格操作）
  4. `判断S[i]和T[j]是否相等，若不相等则return false，若相等则执行i--、j--`
  5. `当外层while循环结束时，直接return true`

## 答

1. 定义正则表达式`let reg = /\w#/g`
2. `通过while循环，不断的将S、T字符串中的空格和空格前面的字符抵消掉（置换成''），直到字符串中不包含空格时退出循环（若空格在字符串首位，则直接去掉该空格）`
3. `判断最终两个字符串是否相等，若相等返回true，否则返回false`

```javascript
var backspaceCompare = function(S, T) {
  let reg = /\w#/g
  let s1 = S.replace(reg, '')
  let t1 = T.replace(reg, '')
  while(s1.indexOf('#') !== -1 || t1.indexOf('#') !== -1) {
    if(s1[0] === '#') {
      s1 = s1.slice(1)
    }
    if(t1[0] === '#') {
      t1 = t1.slice(1)
    }
    s1 = s1.replace(reg, '')
    t1 = t1.replace(reg, '')
  }
  if(s1 === t1) {
    return true
  }
  return false
};


//进阶
var backspaceCompare = function(S, T) {
  let i = S.length - 1
  let j = T.length - 1
  let backspaceS = 0
  let backspaceT = 0
  while(i >= 0 || j >= 0) {
    while(i >= 0) {
      if(S[i] === '#') {
        backspaceS++
        i--
      } else if(backspaceS > 0) {
        i--
        backspaceS--
      } else {
        break
      }
    }
    while(j >= 0) {
      if(T[j] === '#') {
        backspaceT++
        j--
      } else if(backspaceT > 0) {
        j--
        backspaceT--
      } else {
        break
      }
    }
    if (S[i] !== T[j]) {
      return false
    }
    i--
    j--
  }
  return true
};
```

# 四十六、水果成篮

## 问

在一排树中，第 i 棵树产生 tree[i] 型的水果。
你可以从你选择的任何树开始，然后重复执行以下步骤：

* 把这棵树上的水果放进你的篮子里。如果你做不到，就停下来。
* 移动到当前树右侧的下一棵树。如果右边没有树，就停下来。

请注意，在选择一颗树后，你没有任何选择：你必须执行步骤 1，然后执行步骤 2，然后返回步骤 1，然后执行步骤 2，依此类推，直至停止。

你有两个篮子，每个篮子可以携带任何数量的水果，但你希望每个篮子只携带一种类型的水果。
用这个程序你能收集的水果总量是多少？

## 示例

```javascript
输入：[1,2,1]
输出：3
解释：我们可以收集 [1,2,1]。
示例 2：

输入：[0,1,2,2]
输出：3
解释：我们可以收集 [1,2,2].
如果我们从第一棵树开始，我们将只能收集到 [0, 1]。
示例 3：

输入：[1,2,3,2,2]
输出：4
解释：我们可以收集 [2,3,2,2].
如果我们从第一棵树开始，我们将只能收集到 [1, 2]。
示例 4：

输入：[3,3,3,1,2,1,1,2,3,3,4]
输出：5
解释：我们可以收集 [1,2,1,1,2].
如果我们从第一棵树或第八棵树开始，我们将只能收集到 4 个水果。
```

**提示：**

* `1 <= tree.length <= 40000`
* `0 <= tree[i] < tree.length`

## 答

1. 定义一个`max和count`变量用以实时存储篮子中水果的最大数量和当前篮子中水果的数量。
2. 定义`prev1、prev2`两种水果
3. 定义一个`newValue`变量用以存储，最后放入篮子中的水果的种类
4. 定义一个`right`变量用以存储，最后连续采摘相同种类水果的数量
5. 从第二种果树处对剩余果树进行遍历
6. 如果出现第三种水果，则对`max、count、prev1、prev2、newValue`进行相应的更新
7. 否则继续对`count++`，并且更新`right和newValue`
8. 遍历结束之后，`return Math.max(count, max)`

```javascript
var totalFruit = function(tree) {
  let max = 0
  let count = 0
  let prev1 = tree[0]
  let prev2
  for(let i = 1; i < tree.length; i++) {
    if(tree[i] !== prev1) {
      prev2 = tree[i]
      count = i + 1
      break
    }
  }
  let right = 1
  let newValue = prev2
  for(let i = count; i < tree.length; i++) {
    if(prev1 !== tree[i] && prev2 !== tree[i]) {
      max = Math.max(max, count)
      count = 1 + right
      right = 1
      prev1 = newValue
      prev2 = tree[i]
      newValue = tree[i]
      continue
    }
    if(newValue === tree[i]) {
      right++
    }else {
      right = 1
    }
    newValue = tree[i]
    count++
  }
  return Math.max(count, max)
};
```

# 四十七、按奇偶排序数组

## 问

给定一个非负整数数组 A，返回一个数组，在该数组中， A 的所有偶数元素之后跟着所有奇数元素。

你可以返回满足此条件的任何数组作为答案。

## 示例

```javascript
输入：[3,1,2,4]
输出：[2,4,3,1]
输出 [4,2,3,1]，[2,4,1,3] 和 [4,2,1,3] 也会被接受。
```

**提示：**

* `1 <= A.length <= 5000`
* `0 <= A[i] <= 5000`

## 答

### 解法一

1. 定义一个`oddArr`数组，用来存储`A`数组中所有的偶数元素
2. 遍历`A`数组，将偶数元素添加到`oddArr`数组中，并且将该元素剔除出`A`数组
3. 遍历结束之后，`return oddArr.concat(A)`

```javascript
var sortArrayByParity = function(A) {
  let oddArr = []
  for(let i = 0; i < A.length; i++) {
    if(A[i] % 2 === 0) {
      oddArr.push(A[i])
      A.splice(i, 1)
      i--
    }
  }
  return oddArr.concat(A)
};
```

### 解法二

不创建其他数组，只在原数组身上操作，节省空间

1. `定义i、j两个指针，初始值分别为0、length-1`
2. 执行`while循环，当i>=j`时退出循环。在循环中，通过`i、j`两个指针不断的查找奇数元素和偶数元素，并进行换位
3. 遍历结束之后，`return A`

```javascript
var sortArrayByParity = function(A) {
  let i = 0
  let j = A.length - 1
  while(i < j) {
    if(A[i] % 2 === 1 && A[j] % 2 === 0) {
      [A[i], A[j]] = [A[j], A[i]]
    }
    if(A[i] % 2 === 0) {
      i++
    }
    if(A[j] % 2 === 1) {
      j--
    }
  }
  return A
}
```

# 四十八、按奇偶排序数组II

## 问

给定一个非负整数数组 `A， A` 中一半整数是奇数，一半整数是偶数。

对数组进行排序，以便当 `A[i]` 为奇数时，`i` 也是奇数；当 `A[i]` 为偶数时， `i` 也是偶数。

你可以返回任何满足上述条件的数组作为答案。

## 示例

```javascript
输入：[4,2,5,7]
输出：[4,5,2,7]
解释：[4,7,2,5]，[2,5,4,7]，[2,7,4,5] 也会被接受。
```

**提示：**

* `2 <= A.length <= 20000`
* `A.length % 2 == 0`
* `0 <= A[i] <= 1000`

## 答

### 解法一

1. `创建一个arr数组，定义odd、even变量，初始值分别为0、1`
2. `遍历A数组，当出现偶数元素时，将其填充到arr数组even下标处，并对even进行+=2操作；当出现奇数时，将其填充到arr数组odd下标处，并对odd进行+=2从操作。`
3. 遍历结束之后，`return arr`

```javascript
var sortArrayByParityII = function(A) {
  let arr = [];
  let odd = 0;
  let even = 1;
  for (let i = 0; i < A.length; i++) {
    if (A[i] % 2 ==0) {
      arr[even] = A[i];
      even = even + 2;
    } else {
      arr[odd] = A[i];
      odd = odd + 2;
    }
  }
  return arr;     
};
```

### 解法二

不创建其他数组，只在原数组身上操作，节省空间

1. `定义一个j变量，初始值为1`
2. 以`2`为一个单位遍历`A`数组，当出现偶数下标处为奇数元素时，通过`while`循环在`A`数组中查找奇数下标处为偶数元素的元素，并将其两者进行换位
3. 遍历结束之后，直接`return A`

```javascript
var sortArrayByParityII = function(A) {
  let j = 1
  for(let i = 0; i < A.length; i += 2) {
    if(A[i] % 2 === 1) {
      while(A[j] % 2 === 1) {
        j += 2
      }
      [A[i], A[j]] = [A[j], A[i]]
    }
  }
  return A
};
```

# 四十九、字符串解码

## 问

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: `k[encoded_string]`，表示其中方括号内部的 `encoded_string` 正好重复 k 次。注意 k 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入。

## 示例

```javascript
s = "3[a]2[bc]", 返回 "aaabcbc".
s = "3[a2[c]]", 返回 "accaccacc".
s = "2[abc]3[cd]ef", 返回 "abcabccdcdcdef".
```

## 答

1. `创建一个numStack数组，用作存储倍数num的栈`，`创建一个strStack数组，用作存储字符result的栈`
2. 对`s`字符串进行遍历。当遇到数字时，实时记录`num`；当遇到`'['`时，将`result和num分别入栈并将result、num`置为空和零；当遇到`']'`时，对`numsStack`进行出栈，并执行`result = strStack.pop() + result.repeat(copyNum)`；其他情况，则执行`result+=str`
3. 遍历结束之后，`return result`

```javascript
var decodeString = function(s) {
  let numStack = [] 	// 倍数num的等待栈
  let strStack = [] 	// 待拼接的str的等待栈
  let num = 0					// 倍数的“搬运工”
  let result = ''			// 字符串的“搬运工”
  for(let str of s) {
    if(!isNaN(str)) {		// 遇到数字
      num = num * 10 + +str		// js中+可以将数字字符转为数字
    } else if(str === '[') {  // 入栈的时机
      strStack.push(result)		// result进入strStack栈等待
      result = ''							// 完成进栈后 清零
      numStack.push(num)			// 倍数num进入栈等待
      num = 0									// 完成进栈后 清零
    } else if(str === ']') {	// 出栈的时机，两个栈的栈顶出栈
      let copyNum = numStack.pop()		//获取拷贝次数
      result = strStack.pop() + result.repeat(copyNum)  // 构建子串
    } else {				// 遇到字母，追加给result串
      result += str
    }
  }
  return result
}
```

