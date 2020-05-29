```java
scenario: we need to maintain the first K values.
If K is larger than 2, it will be a little bit
inconvenience to adjust the sequence

With limited size priority queue, it can be easily fixed.

import java.util.*;

PriorityQueue<String> pQueue = 
        new PriorityQueue<String>();
        // if descending, add Collections.reverseOrder()
        new PriorityQueue<String>(Collections.reverseOrder());

// when need to add:
if (pQueue.size() < LIMIT) {
    pQueue.offer(value);
} else {
    int top = pQueue.poll();
    int top = top > value? top:value;
    pQueue.offer(top);
    
}
```