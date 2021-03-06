# Permutation - 排列

排列常见的有数字全排列，字符串排列等。

## Permutations - 全排列

Question: [(15) Permutations](http://www.lintcode.com/en/problem/permutations/)

```
Given a list of numbers, return all possible permutations.

Example
For nums [1,2,3], the permutaions are:

[

    [1,2,3],

    [1,3,2],

    [2,1,3],

    [2,3,1],

    [3,1,2],

    [3,2,1]

]

Challenge
Do it without recursion
```

#### 题解

使用之前subsets的模板，但是在取结果时只能取`list.size() == nums.size()`的解，且在添加list元素的时候需要注意除重。

**C++**
```c++
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    vector<vector<int> > permute(vector<int> nums) {
        vector<vector<int> > result;
    	if (nums.empty()) {
    	    return result;
    	}

    	vector<int> list;
    	backTrack(result, list, nums);

    	return result;
    }

private:
    void backTrack(vector<vector<int> > &result, vector<int> &list, \
                   vector<int> &nums) {
        if (list.size() == nums.size()) {
            result.push_back(list);
            return;
        }

        for (int i = 0; i != nums.size(); ++i) {
            // remove the element belongs to list
            if (find(list.begin(), list.end(), nums[i]) != list.end()) {
                continue;
            }
            list.push_back(nums[i]);
            backTrack(result, list, nums);
            list.pop_back();
        }
    }
};
```

#### 源码分析

在除重时使用了标准库`find`(不可使用时间复杂度更低的`binary_search`，因为`list`中元素不一定有序)，时间复杂度为 $$O(N)$$, 也可使用`hashmap`记录`nums`中每个元素是否被添加到`list`中，这样一来空间复杂度为 $$O(N)$$, 查找的时间复杂度为 $$O(1)$$.

在`list.size() == nums.size()`时，已经找到需要的解，及时`return`避免后面不必要的`for`循环调用开销。

使用回溯法解题的**关键在于如何确定正确解及排除不符条件的解(剪枝)**。

## Unique Permutations

Question: [(16) Unique Permutations](http://www.lintcode.com/en/problem/unique-permutations/)
```
Given a list of numbers with duplicate number in it. Find all unique permutations.

Example
For numbers [1,2,2] the unique permutations are:

[

    [1,2,2],

    [2,1,2],

    [2,2,1]

]

Challenge
Do it without recursion.
```
#### 题解

在上题的基础上进行剪枝，剪枝的过程和 [Subsets | Algorithm](http://algorithm.yuanbin.me/backtracking/subsets.html) 中的 Unique Subsets 一题极为相似。为了便于分析，我们可以先分析简单的例子，以 $$[1, 2_1, 2_2]$$ 为例。按照上题 Permutations 的解法，我们可以得到如下全排列。

1. $$[1, 2_1, 2_2]$$
2. $$[1, 2_2, 2_1]$$
3. $$[2_1, 1, 2_2]$$
4. $$[2_1, 2_2, 1]$$
5. $$[2_2, 1, 2_1]$$
6. $$[2_2, 2_1, 1]$$

从以上结果我们注意到`1`和`2`重复，`5`和`3`重复，`6`和`4`重复，从重复的解我们可以发现其共同特征均是第二个 $$2_2$$ 在前，而第一个 $$2_1$$ 在后，因此我们的**剪枝方法为：对于有相同的元素来说，我们只取不重复的一次。**嗯，这样说还是有点模糊，下面以 $$[1, 2_1, 2_2]$$ 和 $$[1, 2_2, 2_1]$$ 进行说明。

首先可以确定 $$[1, 2_1, 2_2]$$ 是我们要的一个解，此时`list`为  $$[1, 2_1, 2_2]$$, 经过两次`list.pop_back()`之后，`list`为 $$[1]$$, 如果不进行剪枝，那么接下来要加入`list`的将为 $$2_2$$, 那么我们剪枝要做的就是避免将 $$2_2$$ 加入到`list`中，如何才能做到这一点呢？我们仍然从上述例子出发进行分析，在第一次加入 $$2_2$$ 时，相对应的`visited[1]`为`true`(对应 $$2_1$$)，而在第二次加入 $$2_2$$ 时，相对应的`visited[1]`为`false`，因为在`list`为 $$[1, 2_1]$$ 时，执行`list.pop_back()`后即置为`false`。

一句话总结即为：在遇到当前元素和前一个元素相等时，如果前一个元素`visited[i - 1] == false`,  那么我们就跳过当前元素并进入下一次循环，这就是剪枝的关键所在。另一点需要特别注意的是这种剪枝的方法能使用的前提是提供的`nums`是有序数组，否则无效。

**C++**
```c++
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: A list of unique permutations.
     */
    vector<vector<int> > permuteUnique(vector<int> &nums) {
        vector<vector<int> > ret;
        if (nums.empty()) {
            return ret;
        }

        // important! sort before call `backTrack`
        sort(nums.begin(), nums.end());
        vector<bool> visited(nums.size(), false);
        vector<int> list;
        backTrack(ret, list, visited, nums);

        return ret;
    }

private:
    void backTrack(vector<vector<int> > &result, vector<int> &list, \
                   vector<bool> &visited, vector<int> &nums) {
        if (list.size() == nums.size()) {
            result.push_back(list);
            // avoid unnecessary call for `for loop`, but not essential
            return;
        }

        for (int i = 0; i != nums.size(); ++i) {
            if (visited[i] || (i != 0 && nums[i] == nums[i - 1] \
                && !visited[i - 1])) {
                continue;
            }
            visited[i] = true;
            list.push_back(nums[i]);
            backTrack(result, list, visited, nums);
            list.pop_back();
            visited[i] = false;
        }
    }
};
```

#### 源码解析

Unique Subsets 和 Unique Permutations 的源码模板非常经典！建议仔细研读并体会其中奥义。

后记：在理解 Unique Subsets 和 Unique Permutations 的模板我花了差不多一整天时间才基本理解透彻，建议在想不清楚某些问题时先分析简单的问题，在纸上一步一步分析直至理解完全。

## Reference

- [九章算法 | Permutation II](http://www.jiuzhang.com/solutions/permutations-ii/)
