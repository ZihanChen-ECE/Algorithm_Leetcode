https://leetcode.com/problems/design-bounded-blocking-queue/

必考题 必须会

```cpp
class BoundedBlockingQueue {
private:
    queue<int> q;
    int cap;
    int cnt;
    mutex mu;
    condition_variable not_empty_cv;
    condition_variable not_full_cv;
    
public:
    BoundedBlockingQueue(int capacity) {
        q = queue<int>();
        cap = capacity;
        cnt = 0;
    }
    
    BoundedBlockingQueue(const BoundedBlockingQueue& rhs) = delete;
    BoundedBlockingQueue& operator=(const BoundedBlockingQueue& rhs) = delete;
    
    void enqueue(int element) {
        {
            unique_lock<mutex> lock(mu);
            not_full_cv.wait(lock, [=](){
                return cnt < cap;
            });
            q.push(element);
            cnt++;
        }
        not_empty_cv.notify_one();
    }
    
    int dequeue() {
        unique_lock<mutex> lock(mu);
        not_empty_cv.wait(lock, [=](){
            return cnt > 0;
        });
        int ret = q.front();
        q.pop();
        cnt --;
        lock.unlock();
        
        not_full_cv.notify_one();
        return ret;
    }
    
    int size() {
        unique_lock<mutex> lock(mu);
        return cnt;
    }
};
```