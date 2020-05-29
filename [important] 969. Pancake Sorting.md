https://leetcode.com/problems/pancake-sorting/

煎饼排序，每次reverse [0:k] 的元素，问如何选择k使得list能被sort

思路为每次找当前slice最大的值(初始slice为整个list)，找到以后(e.g., idx) reverse一次from begin to that idx
```cpp
reverse(A.begin(), A.begin() + idx + 1);
```
然后再reverse一次 from begin to the cur slice，使得当前最大值沉底, i为当前的iter
```cpp
reverse(A.begin(), A.end() - i);
```
复杂度: O(N^2)

解法
```cpp
class Solution {
public:
    vector<int> pancakeSort(vector<int>& A) {
        // flip means reverse the slice
        vector<int> ret;
        for(int i = 0; i < A.size(); i++) {
            int idx = findMax(A, A.size() - i);
            if (idx != 0) {
                reverse(A.begin(), A.begin() + idx + 1);
                ret.push_back(idx+1);
            }
            // move the last to end
            reverse(A.begin(), A.end() - i);
            ret.push_back(A.size()-i);
            
        }
        //traverse(A);
        return ret;
    }
    
    int findMax(vector<int>& A, int end) {
        int idx = 0;
        int local = A[0];
        for (int i = 0; i < end; i++) {
            if (A[i] > local) {
                local = A[i];
                idx = i;
            }
        }
        return idx;
    }
    
    void traverse(vector<int>& A) {
        for (auto a: A) cout << a << " ";
        cout << endl;
    }
};
```
