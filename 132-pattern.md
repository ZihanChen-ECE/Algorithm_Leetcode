https://leetcode.com/problems/132-pattern/

i对应的数是最小的，所以可以用j left的最小值来记录，只用两个loop即可

```java
class Solution {
    public boolean find132pattern(int[] nums) {
        if (nums.length < 3) return false;
        int first =  nums[0];
        for(int j = 1; j < nums.length; j++) {
            for (int k = j + 1; k < nums.length; k++ ) {
                if (first < nums[k] && nums[k] < nums[j]) return true;
                else {
                    first = Math.min(first, nums[j]);
                }
            }
        }
        return false;
    }
}
```