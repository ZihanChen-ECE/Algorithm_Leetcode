https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/

背包问题

```java
class Solution {
    Integer [][] map;
    int mod = (int)1e9+7;
    public int numRollsToTarget(int d, int f, int target) {
        map = new Integer[d+1][target+1];
        map[0][0] = 1;
        return helper(d, f, target);        
    }
    
    public int helper( int idx, int f, int target) {
        if (idx < 0 || target < 0) return 0;
        if (map[idx][target] != null) return map[idx][target];
        long local = 0;
        for (int i = 1; i <= f; i++) {
            local += helper(idx - 1, f, target - i) % mod;
        }
        map[idx][target] = (int)(local%mod);
        return map[idx][target];
    }
}

```