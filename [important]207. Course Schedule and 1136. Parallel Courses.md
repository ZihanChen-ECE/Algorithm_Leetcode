https://leetcode.com/problems/course-schedule/

思路：有向图判定circle
1. Backtracking。不同于无向图，有向图必须用backtracking，因为起点是不确定的！这就导致O(n2)而不是O(n)的复杂度
2. topology sort 拓扑排序可以O(n+e) 完成。其思路为：
```
(1) 寻找indegree为0的点，记录这个点，并删除这个点的outdegree；
(2) 再次检查是否有indegree为0的点，重复（1）
```
solution:

拓扑排序 + TE solution (py会TE, cpp不会)
```cpp
class Solution {
private:
    unordered_map<int, unordered_set<int>> graph;
    int* ids;
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        return TPS(numCourses, prerequisites);
    }
    
    bool TPS(int numCourses, vector<vector<int>>& prerequisites) {
        // Topological sorting
        int* indegree = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            indegree[i] = 0;
        }
        vector<int> popped_nodes;
        queue<int> queue;
        for (auto& vec: prerequisites) {
            if (graph.find(vec[1]) == graph.end()) {
                graph[vec[1]] = unordered_set<int>();
            }
            graph[vec[1]].insert(vec[0]);
            indegree[vec[0]] ++;
        }
        // firstly original 0 indegree nodes
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                queue.push(i);
            }
        }
        
        while (!queue.empty()) {
            int cur = queue.front();
            popped_nodes.push_back(cur);
            queue.pop();
            // notify the indegree
            if (graph.find(cur) == graph.end()) {
                // isolated node
                continue;
            } else {
                for (auto ch: graph[cur]) {
                    indegree[ch] --;
                    if (indegree[ch] == 0) queue.push(ch);
                }
            }
        }
        
        // check popped_nodes
        return popped_nodes.size() == numCourses;
         
    }
    
    bool TE(int numCourses, vector<vector<int>>& prerequisites) {
        // directde graph detect circle
        for (auto& vec: prerequisites) {
            int& key = vec[0];
            if (graph.find(key) == graph.end()) {
                graph[key] = unordered_set<int>();
            }
            graph[key].insert(vec[1]);
        }
        ids = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            ids[i] = 0;
        }
        bool ret = true;
        for (int i = 0; i < numCourses && ret; i++) {
            if (!dfs(i)) {
                ret = false;
            }
        }
        
        delete[] ids;
        ids = nullptr;
        
        return ret;
    }
    // needs back tracking
    bool dfs(int cur) {
        bool ret = true;
        ids[cur] = 1;
        for (auto ch: graph[cur]) {
            if (ids[ch] == 1 || !dfs(ch)) {
                ret = false;
                break;
            }
        }
        ids[cur] = 0;
        return ret;
    }

};
```

补一个1136. Parallel Courses
https://leetcode.com/problems/parallel-courses/

给一个relations，relations[i][j]表示course j depends on course i。现在每个semester都可以完成任意一门没有dep的course，求问多少个semester可以完成（如果完成不了，则return -1）


思路：依然topological sort。唯一要注意的就是在每个semester里需要把queue empty（所有没有dep的course都要take）

```cpp
class Solution {
public:
    int minimumSemesters(int N, vector<vector<int>>& relations) {
        // topological sort
        vector<int> dep(N+1, 0);
        int cnt = 0;
        unordered_map<int, unordered_set<int>> childs;
        for (auto vec: relations) {
            dep[vec[1]]++;
            childs[vec[0]].insert(vec[1]);
        }
        queue<int> q;
        for (int i = 1; i < N+1; i++) {
            if (dep[i] == 0) {
                q.push(i);
                dep[i] = -1;
            }
        }
        int sem = 0;
        while (cnt < N) {
            if (q.empty()) {
                //cout << sem << " " << cnt << endl;
                return -1;
            }
            sem++;
            while(! q.empty()) {
                int cur = q.front();
                q.pop();
                for (auto ch: childs[cur]) {
                    dep[ch] --;
                }
                cnt++;
            }
            for (int i = 1; i < N+1; i++) {
                if (dep[i] == 0) {
                    q.push(i);
                    dep[i] = -1;
                }
            }
        }
        return sem;
    }
};
```