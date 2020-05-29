Given a collection of distinct integers, return all possible permutations.
```
Input: [1,2,3]
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

思路：backtracking，需要配合visited。这里注意的是如何用constructor生成vec

```cpp
class Solution {
private:
    vector<vector<int>> ret;
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<int> temp;
        vector<int> visited(nums.size(), 0);
        dfs(nums, visited, temp);
        return ret;
    }
    
    void dfs(vector<int>& nums, vector<int>& visited, vector<int>& temp) {
        if (temp.size() == nums.size()) {
            vector<int> vec = vector<int> (temp);
            ret.push_back(vec);
        } else {
            for (int i = 0; i < nums.size(); i++) {
                if (visited[i] == 0) {
                    int idx = temp.size();
                    temp.push_back(nums[i]);
                    visited[i] = 1;
                    dfs(nums, visited, temp);
                    visited[i] = 0;
                    temp.erase(temp.begin() + idx);
                }
            }
        }
    }
};
```