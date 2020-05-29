https://leetcode.com/problems/employee-free-time/

这类问题统一用sweep line来做

思路：先整体sort，再取一个t一路sweep through所有的intervals。有以下两种情况：
1. t.end < interval.start, 表示此处有空隙，可以走，同时t主动重置为interval
2. 如果大于，则看t.end < intervel.end, 谁的end靠后，t就应该被set为谁

```c++
/*
// Definition for an Interval.
class Interval {
public:
    int start;
    int end;

    Interval() {}

    Interval(int _start, int _end) {
        start = _start;
        end = _end;
    }
};
*/
#include <vector>
#include <algorithm>
class Solution {
public:
    vector<Interval> employeeFreeTime(vector<vector<Interval>> schedule) {
        // a timestamp is considered to be occupied as:
        // t is [lefy, right)i
        // firstly convert the 2d vec to 1d, sorted by the start
        vector<Interval> full;
        for (auto emp: schedule) {
            for (auto inv: emp) {
                full.push_back(inv);
            }
        }
        sort(full.begin(), full.end(), [](Interval &it1, Interval &it2) {
            return it1.start < it2.start;
        });
        vector<Interval> ret;
        Interval t = full[0];
        for (auto it: full) {
            if (t.end < it.start) {
                ret.push_back(Interval(t.end, it.start));
                t = it;
            } else {
                t = (t.end < it.end) ? it:t;
            }
        }
        return ret;
            
    }
    
};
```