

### Description
> Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.
>
> You may assume no duplicates in the array.
>
> Here are few examples.
>
> `[1,3,5,6]`, 5 → 2
> 
> `[1,3,5,6]`, 2 → 1
> 
> `[1,3,5,6]`, 7 → 4
> 
> `[1,3,5,6]`, 0 → 0

<!--more-->

### Solution

[Search Insert Position](https://oj.leetcode.com/problems/search-insert-position/) is very easy, but the \\(O(log(n))\\) solution requires some attention to avoid bugs.

#### Simple solution : \\(O(n)\\)
A naive solution would be traverse the array, starting from index 0, as long as the target value is larger than value in current index, move index to right by 1.

#### Better solution : \\(O(log(n))\\)
We all know that to search for a particular value in a sorted array, we can use *binary search* to achieve \\(O(log(n))\\) time complexity.

This problem can be solved by binary search too.

Before we start our binary search, we need first to set up the rule, since in this problem we are looking for the index to insert a value, not just an exising value in the array.

From the first example in **Description**, we know that if the value to be inserted exists in the array, we insert it *at* the index of that value, which help us come up with the idea:

**The index for insertion is `i` if the value to be inserted is not greater than the `i`th element and it is greater than the `i-1`st element. i.e. (i-1, i]**

### Summary
Using binary search to locate the position to insert the value

### More
There is one thing we need to pay attention when implement code for this problem (and possibly for all binary search problems): pointers and corresponding stop coniditon.

To simplify the implementation, I initialized `right` to be the size of the array and `left` to be 0, which means each sub-array will contain index range `[left, right)`, and assigned `mid` to be just `(left+right)/2`, which makes sure in following iterations we need just to assign either `left=mid` or `right=mid` instead of `mid-1`.

In this case, the finish test is `left<right-1`, not `left<=right`, because when `left==right-1`, we only have one single element in this sub-array.

The last step is to consider the test to choose to continue on which side. Left or right?

Note that if we choose left side, we will not be able to insert the value at `mid`, so we only choose to continue on left side when target value is smaller than value at `mid` (even if they are equal, we should proceed on right side).

My first attempts are full of errors and got `Compile Error` twice and `Wrong Answer` twice before I finally managed to implement a bug-free version of this algorithm.

Since there are many details to keep an eye on, I'll post my code here as a one-time exception.

```c++
class Solution {
public:
    int searchInsert(int A[], int n, int target) {
        int pos = 0;
        int left = 0;
        int right = n;
        int mid = (left+right)/2;
        while (left<right-1) {
            if (target<A[mid]) {
                right = mid;
                mid = (left+right)/2;
            }
            else {
                left = mid;
                mid = (left+right)/2;
            }
        }
        if (target <= A[left]) {
            return left;
        }
        else {
            return right;
        }
    }
};
```

#### More More
I recently saw a discussion about overflow. Be careful when you calculate `mid`. Using `(left+right)/2` may cause overflow. A safe way to do this is to use `(right-left)/2+left`. This also makes sure left half has the same or one less elements than right half. See the table below:

left | right | mid | [left, mid) | [mid, right)
---|---|---|---|---
3 (odd) | 10 (even) | 6 | [3,4,5] | [6,7,8,9]
3 (odd) | 9 (odd) | 6 | [3,4,5] | [6,7,8]
4 (even) | 9 (odd) | 6 | [4,5] | [6,7,8]
4 (even) | 10 (even) | 7 | [4,5,6] | [7,8,9]
