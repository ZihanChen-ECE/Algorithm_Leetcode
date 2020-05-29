https://leetcode.com/problems/daily-temperatures/

思路：单调栈问题，保持一个单调递减栈来记录右边第一个比当前元素大的元素

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        int N = T.size();
        stack<int> stack;
        vector<int> ret(N, 0);

        // scan the array once and a monotone descending stack
        for (int i = 0; i < N; i++) {
            if (stack.empty()) stack.push(i);
            else if (T[i] <= T[stack.top()]) stack.push(i);
            else {
                while (!stack.empty() && T[i] > T[stack.top()]) {
                    ret[stack.top()] = i - stack.top();
                    stack.pop();
                }
                stack.push(i);
            }
        }
        
        return ret;
    }
};

```

变种：503. Next Greater Element II
https://leetcode.com/problems/next-greater-element-ii/
单调栈扫一遍以后，由于有一些elem可能没有右边比它大的但是有左边比它大的，因此需要再线性扫一遍(from begin to idx)
```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        vector<int> ret(nums.size(), -1);
        stack<int> st;
        int j = 0;
        while(j < nums.size()) {
            if (st.empty()) st.push(j);
            else if (nums[j] <= nums[st.top()]) st.push(j);
            else {
                while(!st.empty() && nums[st.top()] < nums[j]) {
                    int idx = st.top();
                    ret[idx] = nums[j];
                    st.pop();
                }
                st.push(j);
            }
            j++;
        }
        //for (auto nn: ret) cout << nn << " ";
        while (!st.empty()) {
            int idx = st.top();
            for (int i = 0; i < idx; i++) 
                if (nums[i] > nums[idx]) {
                    ret[idx] = nums[i]; 
                    break;
                }
            st.pop();
        }
        return ret;
    }
};
```