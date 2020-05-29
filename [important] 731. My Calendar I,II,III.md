https://leetcode.com/problems/my-calendar-ii/

重要！判定两个区间overlap:
```cpp
v1[0] < v2[1] && v1[1] > v2[0]
```
这题可以keep一个overlap list，每次拿到新的区间，先去和overlap比较一下，再和intervals比较并更新overlap
```cpp
class MyCalendarTwo {
private:
    vector<vector<int>> intervals;
    vector<vector<int>> overlaps;
public:
    MyCalendarTwo() {
        
    }
    
    bool book(int start, int end) {
        // check if the time is conflicting in the overlap array
        for (auto ov: overlaps) {
            if (ov[0] < end && ov[1] > start) return false;
        }
        
        // check if any overlap happen. Is so, add to the overlap array
        for (auto in: intervals) {
            if (in[0] < end && in[1] > start) {
                vector<int> temp = {max(in[0], start), min(in[1], end)};
                overlaps.push_back(temp);
            }
        }
        // add to the interval
        vector<int> temp = {start, end};
        intervals.push_back(temp);
        return true;
    }

};

/**
 * Your MyCalendarTwo object will be instantiated and called as such:
 * MyCalendarTwo* obj = new MyCalendarTwo();
 * bool param_1 = obj->book(start,end);
 */
```

类似题
729. My Calendar I
需要自己写一个sorted linkedlist进行插入

千万千万注意当插入的位置是第一个时，是head=newNode而不是head->next=newNode！
```
class Node{
public:
    int start;
    int end;
    Node* next;
    Node(int s, int e) : start(s), end(e), next(NULL) {}
};

class MyCalendar {
public:
    MyCalendar() {
        head = NULL;
    }
    
    bool book(int start, int end) {
        Node* cur = head;
        // check overlap
        while(cur) {
            //cout << cur->start << " " << cur->end << endl;
            if (cur->start < end && cur->end > start) return false;
            if (cur->start > end) break;
            cur = cur->next;
        }
        // insert to the calender
        cur = head;
        Node *newNode = new Node(start, end);
        if (!cur) head = newNode;
        else {
            Node *prev = NULL;
            bool found = false;
            while(cur && !found) {
                if (cur->start > start) {
                    newNode->next = cur;
                    if (prev) {
                        prev->next = newNode;
                    } else {
                        // head
                        head=newNode;
                    }
                    found = true;
                }
                prev = cur;
                cur = cur->next;
            }
            if (! found ) {
                prev->next = newNode; // prev must be valid
            }
        }
        //traverse();

        return true;
    }
    
    void traverse() {
        Node* cur = head;
        while(cur) {
            cout << cur->start << " " << cur->end << ", ";
            cur = cur->next;
        }
        cout << endl;
    }
     
private:
    Node* head;
};

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar* obj = new MyCalendar();
 * bool param_1 = obj->book(start,end);
 */
```
进一步 732. My Calendar III
https://leetcode.com/problems/my-calendar-iii/

可以一直insert，求overlap区间中具有最多intervals的值

思路：用vector保持sort会超时，因此自己建立一个LL。同时用一个set来记录所有的end，在check overlapped interval时通过iterate through 这个set来判断。并且当cur->start >= e时可以break
```cpp
class Node{
public:
    int start;
    int end;
    Node* next;
    Node(int s, int e) : start(s), end(e), next(NULL) {}
};

class MyCalendarThree {
private:
    unordered_set<int> set;
    vector<vector<int>> calender;
    Node* head;
public:
    MyCalendarThree() {
        head = nullptr;
    }
    
    int book(int start, int end) {
        set.insert(end);
        Node* newnode = new Node(start, end);
        // insert to the list
        if (! head) head = newnode;
        else {
            Node* cur = head;
            Node* prev = NULL;
            while(cur) {
                if (cur->start > start || (cur->start == start && cur->end > end)) {
                    // insert here
                    newnode->next = cur;
                    if (!prev) {
                        // insert as head
                        head = newnode;
                    } else {
                        prev->next = newnode;
                    }
                    break;
                }
                prev = cur;
                cur = cur->next;
            }
            if (!cur) prev->next = newnode;
        }
        
        // search for the overlap
        int k =  0;
        for (auto e: set) {
            int local = 0;
            Node* cur = head;
            while(cur) {
                if (cur->start < e && cur->end >= e) local++;
                else if (cur -> start >= e) break;
                cur = cur->next;
            }
            k = max(k, local);
        }
        return k;
    }
    
    
    int book_TE(int start, int end) {
        set.insert(end);
        vector<int> new_pair = {start, end};
        
        auto it = lower_bound(calender.begin(), calender.end(), new_pair, [](const vector<int>& a, const vector<int>& b) -> bool {
            return (a[0] < b[0])? true : (a[1] < b[0]);
        });
        calender.insert(it, new_pair);
        
        int k = 0;
        int N = calender.size();
        for (auto e: set) {
            int local = 0;
            for (int i = 0; i < N; i++) {
                if (calender[i][0] < e && calender[i][1] >= e) local ++;
            }
            k = max(k, local);
        }
        return k;
    }
};

/**
 * Your MyCalendarThree object will be instantiated and called as such:
 * MyCalendarThree* obj = new MyCalendarThree();
 * int param_1 = obj->book(start,end);
 */
```