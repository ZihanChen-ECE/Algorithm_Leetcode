https://leetcode.com/problems/critical-connections-in-a-network/

解法：参考了

https://www.youtube.com/watch?v=mKUsbABiwBI

利用每个点的child的level来寻找是否有环。一开始老是错，因为在生成map时忘记生成双向了，艹

```c++
#include <algorithm>

class Solution {
private:
    int* minLevel;
    unordered_map<int, vector<int>> map;
public:
    vector<vector<int>> criticalConnections(int n, vector<vector<int>>& connections) {
        // the idea is to record the level of cur node，
        // and compare with both parent and child. 
        // If child < parent, means [parent, cur] is not a critical path
        // use dfs for searching
        
        minLevel = new int[n];
        for (auto pair: connections) {
            if (map.find(pair[0]) == map.end()) {
                // key not in the map, init it first
                map[pair[0]] = vector<int> (); 
            }
            map[pair[0]].push_back(pair[1]);
            
            if (map.find(pair[1]) == map.end()) {
                // key not in the map, init it first
                map[pair[1]] = vector<int> (); 
            }
            map[pair[1]].push_back(pair[0]);
        }
        for (int i = 0; i < n; i++) {
            minLevel[i] = -1;
        }
        vector<vector<int>> ret;
        dfs(0, -1, 0, ret);
        
        delete[] minLevel;
        return ret;
    }
    
    int dfs(int cur, int par, int level, vector<vector<int>>& ret) {
        // returns the min level of all it's children
        minLevel[cur] = level;
        for (auto child: map[cur]) {
            
            if (child == par) continue;
            
            if (minLevel[child] == -1) {
                minLevel[cur] = min(minLevel[cur], dfs(child, cur, level+1, ret));
            } else {
                minLevel[cur] = min(minLevel[cur], minLevel[child]);
            }
        }
        if (minLevel[cur] == level && par != -1 ) {
            // no change, not a cycle
            vector<int> temp = {cur, par};
            ret.push_back(temp);
        }
        return minLevel[cur];
    }
    
};
```


附加一个找critical routers。原理与critical connection一致，就是看当前的depth与child的lowest depth的关系
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <unordered_map>
#include <unordered_set>

using namespace std;

int* lowest;
std::vector<int> ret;
int dfs(int cur, int par, int depth, unordered_map<int, unordered_set<int>>& map);

vector<int> CR(int numNodes, int numEdges, vector<vector<int>>& edges) {

	// generated the connected map
	unordered_map<int, unordered_set<int>> map;
	for (auto edge: edges) {
		map[edge[0]].insert(edge[1]);
		map[edge[1]].insert(edge[0]);
	}

	lowest = new int[numEdges];
	for (int i = 0; i < numEdges; i++) {
		lowest[i] = -1;
	}

	// generating the dfs once, and update the lowest number
	dfs(0, -1, 0, map);
	delete[] lowest;
	lowest = nullptr;
	return ret;
}

int dfs(int cur, int par, int depth, unordered_map<int, unordered_set<int>>& map) {
	lowest[cur] = depth;
	int childs = 0;
	int inserted = 0;
	for (auto ch: map[cur]) {
		if (ch == par) {
			continue;
		} else {
			if (lowest[ch] == -1) {
				// unvisited
				lowest[cur] = min(lowest[cur], dfs(ch, cur, depth+1, map));
				childs++; // childs in the dfs tree
			} else {
				lowest[cur] = min(lowest[cur], lowest[ch]);
			}
			if (par != -1 && lowest[ch] >= depth && inserted == 0) {
				ret.push_back(cur);
				inserted = 1;
			}
		}
	}
	if(par == -1 && childs > 1 && inserted == 0)
		ret.push_back(cur);
	
	return lowest[cur];
}

void solver(int numNodes, int numEdges, vector<vector<int>>& edges) {
	vector<int> rett = CR(numNodes, numEdges, edges);
	for (auto n: rett) {
		cout << n << " "; 
	}
	cout << "\n";
}

int main() {
	// test 1
	vector<vector<int>> edges = {{0, 1}, {0, 2}, {1, 3}, {2, 3}, {2, 5}, {5, 6}, {3, 4}};
	int nN = 7;
	int nE = 7;
	solver(nN, nE, edges);

	// test 2
	ret = vector<int>();
	edges = {{0, 1}, {0, 2}, {1, 2}, {2, 3}, {2, 4}, {3, 4}};
	nN = 5;
	nE = 6;
	solver(nN, nE, edges);
}
```