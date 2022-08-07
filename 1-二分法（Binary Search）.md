# 二分法（一）之基础篇
## 0.关于二分
一个大西瓜，从中间剖两半，丢一半留一半，再从中间剖两半，丢一半留一半，留下的还剩四分之一，再剖再丢。。。直到只剩一口，你决定不能再剖了，于是一口吃掉了剩下的西瓜。没错，这就是鼎鼎大名的二分查找算法了！

## 1.二分法联想
二分法联想：有序～无重复～对数～O(logn)复杂度～优化O(n)～

## 2.二分法基本理解
朋友们，磨刀不误砍柴工，让我们从最经典的二分查找开始吧～

题目链接：[704. 二分查找](https://leetcode.cn/problems/binary-search/)，大概意思是给你一个n个元素的升序数组，跟你保证元素不重复，再随便给你一个值，让你看一下数组里有没有这个值，有的话返回下标，没有的话默认返回-1

说实话，二分查找已经深入人心了，这时候你要是开口就说我想从nums[0]开始按顺序一个一个搜索，估计也说不过去，即没有利用升序的条件也没有满足O(logn)的时间复杂度要求，关键是会很丢人。。。所以直接来看怎么二分查找吧。

二分法的关键就在于二分（= =像废话），每一次查找都可以将查找范围缩小为上一次的二分之一，直到缩无可缩（如数组只剩下一个元素），运气最差的时候，需要分完，也就是说，从n->n/2->n/4->......->1，那么需要分多少次呢？logn次(以2为底)，比如8个元素的时候，分3次就行了，16个元素的时候，分4次就行了。

至此，可以形成思路了，拿到一个数组，每次将其二分，然后判断要找的target在哪一半里面，一直进行下去，直到只剩下一个元素分无可分的时候结束；回想一下程序结构，这里需要**一次又一次**地进行二分，所以需要一个循环结构；看target在哪一半需要条件结构；赋值和返回肯定是要的，接下来就尝试写代码吧～

```python
class Solution:
	def search(self, nums: List[int], target: int) -> int:
		while xxxxx:
			???
```

好吧，写不出来了，莫慌，让我们想得更细一点，先从一次循环开始，我们想先对一个数组二分（分成两部分，假设就叫左边和右边），然后还想判断一个数在左边还是右边，没了。那么怎么将一个数组分成两半呢？想一下数组组成（下标和值），也只有下标可以，比如nums[]有4个元素，那么nums[0:2]是左边，nums[2:4]是右边，如果是奇数个元素，分成两半中间正好还多出来一个；那么怎么判断target在左边还是右边呢？注意nums是一个升序排列的数组，我们没有必要一个一个去比对，nums最左边的数最小、最右边的数最大，分成两半后，分别在左边和右边也遵守这个规律，因此，我们只需要借助中间值和target来比较，就可以知道target可能在哪边。由此，我们需要定义一个中间值，而中间值又由左边界和右边界确定，所以还要定义左右边界，然后在判断的时候，要么大于，要么小于，要么相等，如果运气好相等了，那么恭喜你，找到了target！直接返回下标就好了
```python
left, right = 0, len(nums) - 1
mid = (left + right) // 2
if target > nums[mid]:
	xxx
elif target < nums[mid]:
	xxx
else:
	return mid
```

那么大于或者小于的时候，要做什么呢？当target > nums[mid]的时候，代表target在数组右边；反之，target在数组左边，接下来要做的就是分别在右边或者左边再去二分，因此，需要将左右边界更新一下，更新为左边或者右边对应的边界。

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] > target:
                right = mid - 1
            elif nums[mid] < target:
                left = mid + 1
            else:
                return mid 
        return -1 
```

TODO:
1. 需要画几个图，建立二分查找的感性认识（如果target小，整个区间会往数组左边跑，反之，往右边跑）
	- 感性认识1——区间在向target附近靠拢；
	- 感性认识2——
2. 需要考虑下大数越界，优化mid的计算
3. 需要讨论两种写法，讲一下循环不变量
4. 实数的二分法

# 二分法（二）之强化篇
[35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

思路：
拿到这个题很熟悉，跟基本的二分法很像了，只是多了一步，不在数组中的target要返回其应该被插入的位置（下标）。比如[1, 3, 5, 7]，target = 2，找不到，应该被插入1的后面，数组就变成[1, 2, 3, 5, 7]，2的下标就是1，所以就返回1。关于这个插入位置，隐隐猜测是要分情况讨论，回想下之前二分法的左右边界和中间值，就慢慢想清楚了在找不到target的情况下这里的插入位置总共有3种情况：a.target比所有值都小，要被放到数组最左边；b.target比所有值都大，要被放到数组最右边；c.target在左右边界中间，要放到比它小的值右边。然后就可以看下每种情况下要返回啥了：
```
case 1 target比所有值都小 ，二分法找啊找，最后一步是left等于right且都等于0，没得分了，但还是要进行最后一次循环判断，target小于最后一个元素，所以按照条件，right要减1，right就跑到left左边去了，下一次while循环条件就不满足退出循环了，target要放在nums[0]，即返回left or right+1
case 2 target比所有值都大，二分法找到最后，left也等于right了，且都等于n-1(n是数组长度)了，根据条件，最后一次循环会让left加1，left就跑到right右边去了，而target要放在nums[n]，所以也是返回left or right+1
case 3 target在中间，举个例子，最后数组切到只剩两个元素了[2, 4]，target是3，所以一定是右边界往左靠，最后left和right都相等且等于2所在的下标（假如是6），然后条件判断一定会将left加1（等于7），超过right（等于6），而target要被插入到2的后面，因此返回left or right + 1
```
好了，情况讨论完了，可以看到，最后的返回结果统一起来了，就很舒服。返回left or right+1就可以啦！
代码：
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] > target:
                right = mid - 1
            elif nums[mid] < target:
                left = mid + 1
            else:
                return mid 
        return left 
```

---

[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

思路：
其实在学二分法的时候，我就在想，如果二分法关键的两个条件（1.数组升序排列；2.元素无重复）不满足怎么办？这个题马上就戳中了这个点，拿掉了【元素无重复】这个条件，让我们找找看target重复了多少次。

代码：
想法一：随便找到一个，然后往左右两边找
```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        left ,right = 0, len(nums) - 1
        index = -1
        res = [0, len(nums) - 1]
        while left <= right:
            mid = (left + right) // 2
            if target > nums[mid]:
                left = mid + 1
            elif target < nums[mid]:
                right = mid - 1
            else:
                index = mid
                break 
        if index == -1:
            return [-1, -1]
        for i in range(index, -1, -1):
            if nums[i] != target:
                res[0] = i + 1
                break 
        for i in range(index, len(nums), 1):
            if nums[i] != target:
                res[1] = i - 1
                break 
        return res
```

想法二：直接找左右边界
```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        if not nums:
            return [-1, -1]
        res = [-1, -1]
        left, right = 0, len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] >= target:
                right = mid - 1
            elif nums[mid] < target:
                left = mid + 1
        if left >= 0 and left <= len(nums) - 1 and nums[left] == target:
            res[0] = left

        left, right = 0, len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] > target:
                right = mid - 1
            elif nums[mid] <= target:
                left = mid + 1
        if right >= 0 and right <= len(nums) - 1 and nums[right] == target:
            res[1] = right
        return res
```

---

 [69. x 的平方根](https://leetcode.cn/problems/sqrtx/)

题目规定了结果保留整数部分，从实例中思考，应该每次二分去找这个数是最快的，于是剩下的就是考虑二分的边界和判断条件细节；边界的话，考虑0 or 1这两个例子的话，还是从[0, x]左闭右闭比较方便，不用额外讨论corner case了，然后需要注意在二分的时候，有可能永远错过结果，比如结果应该是2，结果左边界超过了2来到3，那么永远也不会返回2了，结合题目给的条件，返回的结果永远小于等于x的真正平方根，所以当mid的平方小于x时，需要一个临时变量记录此时的mid，防止越过mid之后再也找不到了。
```python
class Solution:
    def mySqrt(self, x: int) -> int:
        left, right = 0, x
        res = 0
        while left <= right:
            mid = (left + right) // 2
            if mid ** 2 > x:
                right = mid - 1
            elif mid ** 2 < x:
                left = mid + 1
                res = mid 
            else:
                return mid 
        return res 
```

---

[367. 有效的完全平方数](https://leetcode.cn/problems/valid-perfect-square/)

这一题就是上一题的基础上，判断一下是否能找到一个数，其平方等于num，对应二分法的话，就是mid ** 2 = num的条件，此时返回True，其他情况都意味着没找到，都要返回False
```python
class Solution:
    def isPerfectSquare(self, num: int) -> bool:
        left, right = 0, num 
        while left <= right:
            mid = (left + right) // 2
            if mid ** 2 > num:
                right = mid - 1
            elif mid ** 2 < num:
                left = mid + 1
            else:
                return True
        return False
```

---
[374. 猜数字大小](https://leetcode.cn/problems/guess-number-higher-or-lower/)
最多猜$\lceil log_{2}n \rceil$次

```python
# The guess API is already defined for you.
# @param num, your guess
# @return -1 if my number is lower, 1 if my number is higher, otherwise return 0
# def guess(num: int) -> int:

class Solution:
    def guessNumber(self, n: int) -> int:
        left, right = 1, n 
        while left <= right:
            mid = (left + right) // 2
            if guess(mid) == -1:
                right = mid - 1
            elif guess(mid) == 1:
                left = mid + 1
            else:
                return mid 
```


# 二分法（三）之冲刺篇
基础二分：有序、无重复、线性存储
二分思想：每次能排除一半可能
```python
while condition is True:
	if A:
		range -> half range
	else:
		range -> another half range
```

题目list：

[162. 寻找峰值](https://leetcode.cn/problems/find-peak-element/)
这道题想了好久呀，可以画个类似山峰的图，每次切掉一半，和正经的二分结束条件有点不一样，这里绝对不能用等于条件了，不然会陷入死循环
```python
class Solution:
    def findPeakElement(self, nums: List[int]) -> int:
        left, right = 0, len(nums) - 1
        index = 0
        while left < right:
            mid = (left + right) // 2
            if nums[mid] > nums[mid + 1]:
                right = mid
            else:
                left = mid + 1
        return left 
```


[1901. 寻找峰值 II](https://leetcode.cn/problems/find-a-peak-element-ii/)
```python
class Solution:
    def findPeakGrid(self, mat: List[List[int]]) -> List[int]:
        m, n = len(mat), len(mat[0])
        value, index = 0, [0, 0]
        for i in range(m):
            left, right = 0, n - 1
            while left < right:
                mid = (left + right) // 2
                if mat[i][mid] > mat[i][mid + 1]:
                    right = mid 
                else:
                    left = mid + 1
            tmp = mat[i][left]
            if tmp >= value:
                value = tmp
                index = [i, left]                    
            else:
                pass 
        if index[0] - 1 >= 0:
            return index if value > mat[index[0] - 1][index[1]] else [index[0] - 1, index[1]]
        if index[0] + 1 <= m:
            return index if value > mat[index[0] + 1][index[1]] else [index[0] + 1, index[1]]
        return index 
```



[剑指 Offer 11. 旋转数组的最小数字](https://leetcode.cn/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)
[剑指 Offer 04. 二维数组中的查找](https://leetcode.cn/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)
[最接近的二叉搜索树值](https://leetcode.cn/problems/closest-binary-search-tree-value)
[222. 完全二叉树的节点个数](https://leetcode.cn/problems/count-complete-tree-nodes/)






组合题：
[375. 猜数字大小 II](https://leetcode.cn/problems/guess-number-higher-or-lower-ii/)
