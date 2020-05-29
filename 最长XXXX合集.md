1. LCS 最长公共子序列
```cpp
思路很简单，因为是子序列可以不连续
因此只要是找到相同，就能记下来
因此用dp就能简单完成
// c[i][j]: the longest common subsequence for x[0..i] and y[0..j]
// c[0][j] = c[i][0] = 0;
// if (x[i]==y[j]) c[i][j] = c[i-1][j-1] + 1;
// else c[i][j] = max(c[i-1][j], c[i][j-1]);

class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int N1 = text1.size();
        int N2 = text2.size();
        vector<vector<int>> dp = vector(N1+1, vector<int>(N2+1, 0));
        for (int i = 1; i <= N1; i++) {
            for (int j = 1; j <= N2; j++) {
                if(text1[i-1] == text2[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
                }
            }
        }
        return dp[N1][N2];
    }
};

如果使用两行memory
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int N1 = text1.size();
        int N2 = text2.size();
        vector<vector<int>> dp = vector(2, vector<int>(N2+1, 0));
        for (int i = 1; i <= N1; i++) {
            for (int j = 1; j <= N2; j++) {
                if(text1[i-1] == text2[j-1]) {
                    dp[1][j] = dp[0][j-1] + 1;
                } else {
                    dp[1][j] = max(dp[0][j], dp[1][j-1]);
                }
            }
            for (int j = 1; j <= N2; j++) {
                dp[0][j] = dp[1][j];
            }
        }
        return dp[1][N2];
    }
};

```

2. LCSS 最长公共子串
```cpp
这个问题更加简单一些，当x[i] != y[j]时，直接为0, 不再需要check之前
dp[i][j]: x[0..i] 与 y[0..j]时最长公共子串
if (x[i] != y[j])   dp[i][j] = 0;
else dp[i][j] = dp[i-1][j-1] + 1;
dp[0][0] = 0
dp[0][j] = 1 if x[0] == y[j] else 0
dp[i][0] = 1 if x[i] == y[0] else 0

class Solution {
public:
    int longestCommonSubstring(string text1, string text2) {
        int N1 = text1.size();
        int N2 = text2.size();
        vector<vector<int>> dp = vector(N1, vector<int>(N2, 0));
        for (int j = 0; j < N2; j++) {
            if (text1[0] == text2[j])  
                dp[0][j] = 1;
        }
        for (int i = 0; i < N1; i++) {
            if (text1[i] == text2[0])
                dp[i][0] = 1;
        }
        
        for (int i = 1; i < N1; i++) {
            for (int j = 1; j < N2; j++) {
                if(text1[i-1] == text2[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = 0;
                }
            }
        }
        int gmax = 0;
        for (int i = 0; i < N1; i++)
            for (int j = 0; j < N2; j++)
            gmax = max(dp[i][j]);
            
        return gmax;
    }
};

当然，由于这里也只需要i-1， j-1，
因此可以把map改为一个var即可。
当i-1, j-1在边界时，判定是否为1即可

```
