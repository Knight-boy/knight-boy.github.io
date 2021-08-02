---
title:"Stack Algorithm"
date: 2021-08-03
---

 **Next Greater Element II**

Given a circular integer array `nums` (i.e., the next element of `nums[nums.length - 1]` is `nums[0]`), return *the **next greater number** for every element in* `nums`.

The **next greater number** of a number `x` is the first greater number to its traversing-order next in the array, which means you could search circularly to find its next greater number. If it doesn't exist, return `-1` for this number.

**Example 1:**

```
Input: nums = [1,2,1]
Output: [2,-1,2]
Explanation: The first 1's next greater number is 2; 
The number 2 can't find next greater number. 
The second 1's next greater number needs to search circularly, which is also 2.
```

**Example 2:**

```
Input: nums = [1,2,3,4,3]
Output: [2,3,4,-1,4]
```

```c++
class Solution1 {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        ::stack<int> stk;
        ::vector<int> vec(nums.size(), 0);
        for (int i = nums.size() - 1; i >= 0; i--) {
            stk.push(nums[i]);
        }
        for (int j = nums.size() - 1; j >= 0; j--) {
            while (!stk.empty() && (nums[j] >= stk.top())) {
                stk.pop();
            }
            vec[j] = (stk.empty()) ? -1 : stk.top();
            // vec[j] = Alternative(stk.empty(), -1, stk.top());
            stk.push(nums[j]);
        }
        return vec;
    }
};

int main()
{
    Solution1 sol;
    vector<int> nums { 1, 2, 3, 4, 3 };
    vector<int> res;
    res = sol.nextGreaterElements(nums);
    for (const auto &value : res) {
        cout << value << " ";
    }
    return 0;
}
```

