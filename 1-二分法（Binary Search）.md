# 二分法（一）之基础篇
## 0.关于二分
二分查找，老经典的一个算法了，形象理解下：一个大西瓜，从中间剖两半，你手里拿了一半，再从中间剖两半，你手里还剩四分之一，再剖再剖。。。直到剩一口，你决定不能再剖了，于是一口吃掉了剩下的西瓜。

## 1.二分法联想
二分法联想：有序～无重复～对数～O(logn)复杂度～优化O(n)～

## 2.二分法基本理解
从最经典的二分查找开始吧～

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




# 二分法（三）之冲刺篇
