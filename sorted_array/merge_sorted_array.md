# Merge Sorted Array

Question: [(6) Merge Sorted Array](http://www.lintcode.com/en/problem/merge-sorted-array/)

```
Merge two given sorted integer array A and B into a new sorted integer array.

Example
A=[1,2,3,4]

B=[2,4,5,6]

return [1,2,2,3,4,4,5,6]

Challenge
How can you optimize your algorithm if one array is very large and the other is very small?
```

题解：

逐个比较两个数组内的元素，取较大的置于新数组尾部元素中。

```
class Solution {
public:
    /**
     * @param A and B: sorted integer array A and B.
     * @return: A new sorted integer array
     */
    vector<int> mergeSortedArray(vector<int> &A, vector<int> &B) {
        if (A.empty()) {
            return B;
        }
        if (B.empty()) {
            return A;
        }

        vector<int>::size_type sizeA = A.size();
        vector<int>::size_type sizeB = B.size();
        vector<int>::size_type sizeC = sizeA + sizeB;
        vector<int> C(sizeC);

        while (sizeA > 0 && sizeB > 0) {
            if (A[sizeA - 1] > B[sizeB - 1]) {
                C[--sizeC] = A[--sizeA];
            } else {
                C[--sizeC] = B[--sizeB];
            }
        }
        while (sizeA > 0) {
            C[--sizeC] = A[--sizeA];
        }
        while (sizeB > 0) {
            C[--sizeC] = B[--sizeB];
        }

        return C;
    }
};
```

源码分析：

分三种情况遍历比较。

## Merge Sorted Array II

Question: [(64) Merge Sorted Array II](http://www.lintcode.com/en/problem/merge-sorted-array-ii/)

```
Given two sorted integer arrays A and B, merge B into A as one sorted array.

Note
You may assume that A has enough space (size that is greater or equal to m + n) to hold additional elements from B. The number of elements initialized in A and B are mand n respectively.

Example
A = [1, 2, 3, empty, empty] B = [4,5]

After merge, A will be filled as [1,2,3,4,5]
```

题解：

在上题的基础上加入了in-place的限制。将上题的新数组视为length相对较大的数组即可，仍然从数组末尾进行归并，取出较大的元素。

```
class Solution {
public:
    /**
     * @param A: sorted integer array A which has m elements,
     *           but size of A is m+n
     * @param B: sorted integer array B which has n elements
     * @return: void
     */
    void mergeSortedArray(int A[], int m, int B[], int n) {
        int index = n + m;

        while (m > 0 && n > 0) {
            if (A[m - 1] > B[n - 1]) {
                A[--index] = A[--m];
            } else {
                A[--index] = B[--n];
            }
        }
        while (n > 0) {
            A[--index] = B[--n];
        }
        while (m > 0) {
            A[--index] = A[--m];
        }
    }
};
```
