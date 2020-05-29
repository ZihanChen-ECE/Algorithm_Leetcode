https://leetcode.com/problems/partition-to-k-equal-sum-subsets/

一个list能否分割成为K个subset，使得每个subset的和相等

解法肯定为backtracking。为了不TE，使用一个start来记录可以开始的位置

```cpp
class Solution {
private:
    vector<int> visited;
public:
    bool canPartitionKSubsets(vector<int>& nums, int k) {
        //sort(nums.begin(), nums.end());
        int _sum = 0;
        for (auto n: nums) _sum+=n;
        if (_sum % k != 0) return false;
        int target = _sum / k;
        for (auto n: nums) if (n > target) return false;
        visited = vector<int>(nums.size(), 0);
        return dfs(nums, k, target, 0, 0);
    }
    
    bool dfs(vector<int>& nums, int k, int target, int start, int curSum ) {
        if (k == 1) return true;
        if (curSum > target) return false;
        if (curSum == target) return dfs(nums, k - 1, target, 0, 0);
        for (int i= start; i < nums.size(); i++) {
            if (visited[i] == 1) continue;
            visited[i] = 1;
            if (dfs(nums, k, target, i+1, curSum + nums[i])) return true;
            visited[i] = 0;
        }
        return false;
    }
};
```

变种：416. Partition Equal Subset Sum

https://leetcode.com/problems/partition-equal-subset-sum/

为k=2时的特例，可以使用dp, 也就是说如果能取出sum(nums)/2的值，则OK

therefore这又退化成另一个问题，即能否从一个list中挑出某几个元素组成target的背包问题

```
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int _sum = accumulate(nums.begin(), nums.end(), 0);
        int N = nums.size();
        if (_sum & 1) return false;
        int target = _sum >> 1;
        // check if target can be achieved from nums
        vector<bool> dp(target+1, false);
        dp[0] = true;
        for (auto num: nums) {
            for (int i = target; i >= num; i--) {
                dp[i] = dp[i] || dp[i - num];
            }
        }
        return dp[target];
    }
};
```