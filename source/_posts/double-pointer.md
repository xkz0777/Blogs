---
title: 算法之双指针
date: 2021-09-15 14:34:14
tags: 算法
categories: 算法
img: /medias/featureimages/double_pointer.jpg
author: xkz
mathjax: true
top: true
summary: 双指针习题题解
---

# 双指针

近来刷 LeetCode 遇到了不少双指针题，写一篇题解，并尝试总结双指针的规律（仅根据本人经验而言，未必准确，如有错误请读者指出）。

## 什么是双指针

双指针即在遍历数组时，使用两个指针来遍历，可能是两个指针一头一尾往中间走，也可能是两个指针一起往后走等，但遍历的时间往往都是 $O(N)$ 的，在能使用双指针时，通常可以将算法时间复杂度由 $O(N^2)$ 降低为 $O(N)$。

## 两数之和

### 两数之和 II：输入有序数组

链接：https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted。

#### 题目描述

给定一个已按照非递减顺序排列的整数数组 numbers ，请你从数组中找出两个数满足相加之和等于目标数 target 。

函数应该以长度为 2 的整数数组的形式返回这两个数的下标值。numbers 的下标**从 1 开始计数** ，所以答案数组应当满足 `1 <= answer[0] < answer[1] <= numbers.length`。

你可以假设每个输入只对应唯一的答案 ，而且你不可以重复使用相同的元素。

示例 1：

```
输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
```

解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

示例 2：

```
输入：numbers = [2,3,4], target = 6
输出：[1,3]
```

示例 3：

```
输入：numbers = [-1,0], target = -1
输出：[1,2]
```

提示：

1. `2 <= numbers.length <= 3 * 104`
2. `-1000 <= numbers[i] <= 1000`
3. `numbers 按 非递减顺序 排列`
4. `-1000 <= target <= 1000`
5. 仅存在一个有效答案

#### 题解

可以枚举 $O(N^2)$，但显然这浪费了出题人给我们排好的序，用双指针做法可以做到 $O(N)$ 的时间复杂度。

双指针的典型题，用指针 left 指向第一个元素，right 指向第二个元素，记 sum = numbers[left] + numbers[right]。由于数组有序，因此如果 sum 比 target 大则 right--，否则 left++ 。

代码：

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int l = numbers.size();
        int i = 0, j = l - 1;
        while (i < j) {
            int sum = numbers[i] + numbers[j];
            if (sum < target) {
                i++;
            }
            else if (sum > target) {
                j--;
            }
            else {
                return {i + 1, j + 1};
            }
        }
        return {0};
    }
};
```

最后 `return{0};` 只是单纯因为 LeetCode 的编译器要求返回值为 non-void 的函数都要有返回值（这和 Java 还挺像）。

### 两数之和 I：输入无序数组

链接：https://leetcode-cn.com/problems/two-sum

#### 题目描述

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

示例 1：

 ```
 输入：nums = [2,7,11,15], target = 9
 输出：[0,1]
 ```

解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。

示例 2：

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

示例 3：

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

提示：

1. `2 <= nums.length <= 104`
2. `-109 <= nums[i] <= 109`
3. `-109 <= target <= 109`
4. 只会存在一个有效答案

#### 题解

本题其实不属于双指针解法，之所以放在这里是与前一题做对比，双指针法适用的条件，我认为应该是**原数组具有某种顺序特征**，例如这里是数组已经排好了序。

本题的做法是使用哈希表，在遍历时，对于某个元素，先找之前是不是有能和他匹配（即相加得到 target）的元素，如果没有就将其存入哈希表中（顺序一定不能反，否则会导致自己和自己配对的情况）。

代码：

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hashTable;
        int l = nums.size();
        for (int i = 0; i < l; ++i) {
            auto j = hashTable.find(target - nums[i]);
            if (j != hashTable.end()) {  // 插入之前先看是否能配对，避免自己和自己配对。
                return {i, j->second};
            }
            else {
                hashTable[nums[i]] = i;
            }
        }
        return {0};
    }
};
```

## 三数之和

链接：https://leetcode-cn.com/problems/3sum。

### 题目描述

给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

示例 1：

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

示例 2：

```
输入：nums = []
输出：[]
```

示例 3：

```
输入：nums = [0]
输出：[]
```

提示：

1. `0 <= nums.length <= 3000`
2. `-105 <= nums[i] <= 105`


### 题解

最基本的思路是三重循环遍历，枚举所有可能，复杂度为 $O(n^3)$，显然太大了。这时如果用双指针就可以改进算法：先排序，然后遍历第枚举一个数 $first = nums[i]$，则问题转化为在之后的区间 $[i, nums.size())$ 里，找两数之和为 $-first$ 的两个数，总的复杂度为 $O(N^2)$。

代码：

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        int l = nums.size();
        for (int i = 0; i < l; ++i) {
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            int k = l - 1;
            for (int j = i + 1; j < l; ++j) {
                if (j > i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                }
                while (j < k && nums[i] + nums[j] + nums[k] > 0) {
                    k--;
                }
                if (j == k) {
                    break;
                }
                if (nums[i] + nums[j] + nums[k] == 0) {
                    res.push_back(vector<int>{nums[i], nums[j], nums[k]});
                }
            }
        }
        return res;
    }
};
```

这里值得注意的是，由于题给的数组可能会有重复元素，但是返回的三元组不能重复，因此我们还需要有一个去重的判断，即 `nums[i] == nums[i - 1]` 及 `nums[j] == nums[j - 1] ` 的判断，多个连续的重复元素我们只要选取最后一个就可以了。

## 移动零

链接：https://leetcode-cn.com/problems/move-zeroes/。

### 题目描述

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

示例:

```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```


说明:

1. 必须在原数组上操作，不能拷贝额外的数组。
2. 尽量减少操作次数。

### 题解

#### 方法一

因为我们知道数组后面都是 0，因此只要把非 0 的数挨个往前挪就可以了，用指针 `i` 指向已经移动完的部分的末尾（即数组 `[0,i)` 部分都已经是非零的数，用指针 `j` 去遍历数组，当 `j` 碰到非 0 数时，就把 `i` 指向的元素修改为 `j` 指向的元素。

代码：

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int i, j;
        i = j = 0;
        int l = nums.size();
        
        while (j < l) {
            if (nums[j]) {
                nums[i++] = nums[j];
            }
            j++; // 注意这个j++不管有没有进入if语句都要执行，不要写在if语句里面
        }
        
        while (i < l) { // 补0
            nums[i++] = 0;
        }
    }
};
```

#### 方法二

将数组分为三部分。

一、二部分是已经处理好的片段：第一部分全部是非0数，第二部分是已经变成0的数，
第三部分是待处理片段。用指针 `i` 指向第一部分结尾，`j` 指向第二部分结尾，即数组 `[0, i)` 部分为已经完成处理的非 0 数，`[i,j j)` 部分全部为 0，`[j, l)` 部分为待处理部分，初始时 `i` 和 `j` 都指向 0，`j` 向后遍历，遇到非 0 数则与 i 交换。

```cpp
void moveZeroes(vector<int>& nums) {
        int i, j;
        i = j = 0;
        int l = nums.size();
        while (j < l) {  // 当j为l时结束
            if (nums[j]) {
                swap(nums[i++], nums[j++]);
            }
        }
    }
```

