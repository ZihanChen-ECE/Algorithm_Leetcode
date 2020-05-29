https://leetcode.com/problems/kth-largest-element-in-a-stream/

这题主要是记住如何使用pq in c++

```c++
struct Compare {
  bool operator() (const int& a, const int& b) {
      // greater means ascending from front to rear
      return a > b;
  }  
};
class KthLargest {
private:
    priority_queue<int, vector<int>, greater<int> > pq;
    int size;
    int cnt;
public:
    KthLargest(int k, vector<int>& nums) {
        size = k;
        cnt = 0;
        for (int nn: nums) {
            add(nn);
        }
    }
    
    int add(int val) {
        pq.push(val);
        cnt++;
        while (cnt > size) {
            pq.pop();
            cnt --;
        }
        return pq.top();
    }
};

/**
 * Your KthLargest object will be instantiated and called as such:
 * KthLargest* obj = new KthLargest(k, nums);
 * int param_1 = obj->add(val);
 */

```