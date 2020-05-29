465. Optimal Account Balancing
https://leetcode.com/problems/optimal-account-balancing/

思路：backtrack得到global min

```java
class Solution {
    private Map<Integer, Integer> map;
    public int minTransfers(int[][] transactions) {
        map = new HashMap<>();
        for (int[] tran: transactions) {
            map.put(tran[0], map.getOrDefault(tran[0], 0) + tran[2]);
            map.put(tran[1], map.getOrDefault(tran[1], 0) - tran[2]);
        }
        
        List<Integer> list = new ArrayList<>();
        for (int val: map.values()) {
            if (val != 0) list.add(val);
        }
        
        return dfs(0, list);

    }
    
    private int dfs(int idx, List<Integer> list) {
        if (idx >= list.size()) return 0;
        int cur = list.get(idx);
        if (cur == 0) return dfs(idx+1, list);
 
        int min = Integer.MAX_VALUE;
        for (int i = idx+1; i < list.size(); i++) {
            int next = list.get(i);
            if (cur * next < 0) {
                list.set(i, cur+next);
                min = Math.min(min, 1 + dfs(idx + 1, list));
                list.set(i, next);
                if (cur + next == 0) break;
            }
        }
        return min;
    }

}
```