https://leetcode.com/problems/range-sum-query-mutable/

线段树实现，以及singleton pattern的使用 (instance, getInstance()和mu都要是static,并且在global被init)

```cpp
class Node {
public:
    int start;
    int end;
    int val; // the sum from start to end
    Node* left;
    Node* right;
    Node* par;
    Node(int s, int e) : start(s), end(e), val(0), left(nullptr), right(nullptr), par(nullptr) {}
};

class SGTree{
// singleton mode;
private:
    //static SGTree* pInstance;
    //static mutex mu;
    vector<int> & nums;
    Node* head;
    /*
    SGTree(vector<int>& nums) : nums(nums) {
        // build the tree
        buildTree();
    }
    */
    void buildTree() {
        if (nums.size() > 0)
            dfs(0, nums.size() - 1, nullptr);
    }
    
    Node* dfs(int start, int end, Node* par) {
        Node* cur = new Node(start, end);
        if (par == nullptr) {
            head = cur;
        }
        cur->par = par;
        if (start == end) {
            // leaf node
            cur->val = nums[start];
        } else {
            int mid = (start + end) / 2;
            cur->left = dfs(start, mid, cur);
            cur->right = dfs(mid+1, end, cur);
            cur->val = cur->left->val + cur->right->val;
        }
        return cur;
    }
    
public:
    /*
    static SGTree* Instance(vector<int>& nums) {
        if (pInstance == nullptr) {
            lock_guard<mutex> lock(mu);
            if (pInstance == nullptr) {
                pInstance = new SGTree(nums);
            }
        }
        return pInstance;
    }*/
    
    
    SGTree(vector<int>& nums) : nums(nums) {
        // build the tree
        buildTree();
    }
    
    void update(int i, int val) {
        helper(head, i, val);
    }
    
    void helper(Node* root, int i, int val) {
        if(root) {
            //cout << root->val << " " << root->start << " " << root->end <<  endl;
            int s = root->start;
            int e = root->end;
            if (s==e && s==i) {
                root->val = val;
            } else {
                int mid = (s+e)/2;
                if (s <= i && i <= mid) {
                    // left child
                    helper(root->left, i, val);
                } else {
                    helper(root->right, i, val);
                }
                
                root->val = root->left->val + root->right->val;
            }
        }
    }
    
    int sumRange(int i, int j) {
        return helper2(head, i, j);
    }
    
    int helper2(Node* root, int i, int j) {
        if (root) {
            int s = root->start;
            int e = root->end;
            if (s == i && e == j) return root->val;
            else {
                int mid = ( s + e ) /2;
                if(j <= mid) return helper2(root->left, i, j);
                else if (i > mid) return helper2(root->right, i, j);
                else {
                    return helper2(root->left, i, mid) + helper2(root->right, mid+1, j);
                }
            }
        } else {
            return 0;
        }
    }
};

//SGTree* SGTree::pInstance = 0;
//mutex SGTree::mu = mutex();

class NumArray {
private:
    SGTree *Tree;
public:
    NumArray(vector<int>& nums) {
        //Tree = SGTree::Instance(nums);
        Tree = new SGTree(nums);
    }
    
    void update(int i, int val) {
        Tree->update(i, val);
    }
    
    int sumRange(int i, int j) {
        return Tree->sumRange(i, j);
    }
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray* obj = new NumArray(nums);
 * obj->update(i,val);
 * int param_2 = obj->sumRange(i,j);
 */
```