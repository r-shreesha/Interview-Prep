Preparing for an Amazon loop interview involves a deep dive into coding, system design, and behavioral questions. Here are strategies and sample questions for each category:

## 1. Coding (Data Structures & Algorithms)
For this round, focus on fundamental data structures and algorithms. Practice problems on arrays, strings, linked lists, trees, graphs, sorting, searching, and dynamic programming.

### Two Sum
```
vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> map;
    for (int i = 0; i < nums.size(); i++) {
        int complement = target - nums[i];
        if (map.find(complement) != map.end()) {
            return {map[complement], i};
        }
        map[nums[i]] = i;
    }
    return {};
}
```

### Reverse Linked List
```
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    while (head) {
        ListNode* next = head->next;
        head->next = prev;
        prev = head;
        head = next;
    }
    return prev;
}
```

### Valid Parentheses
```
bool isValid(string s) {
    stack<char> stack;
    unordered_map<char, char> map = {{')', '('}, {'}', '{'}, {']', '['}};
    for (char c : s) {
        if (map.find(c) != map.end()) {
            if (stack.empty() || stack.top() != map[c]) return false;
            stack.pop();
        } else {
            stack.push(c);
        }
    }
    return stack.empty();
}
```

### Merge Intervals
```
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    if (intervals.empty()) return {};
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> merged;
    merged.push_back(intervals[0]);
    for (auto& interval : intervals) {
        if (interval[0] <= merged.back()[1]) {
            merged.back()[1] = max(merged.back()[1], interval[1]);
        } else {
            merged.push_back(interval);
        }
    }
    return merged;
}
```

### Binary Tree Level Order Traversal
```
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int size = q.size();
        vector<int> level;
        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front();
            q.pop();
            level.push_back(node->val);
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(level);
    }
    return result;
}
```

### Longest Substring Without Repeating Characters
```
int lengthOfLongestSubstring(string s) {
    vector<int> map(256, -1);
    int left = 0, right = 0, maxLength = 0;
    while (right < s.length()) {
        if (map[s[right]] != -1) {
            left = max(map[s[right]] + 1, left);
        }
        map[s[right]] = right;
        maxLength = max(maxLength, right - left + 1);
        right++;
    }
    return maxLength;
}
```

### Find Median from Data Stream
```
class MedianFinder {
    priority_queue<int> maxHeap; // Left half
    priority_queue<int, vector<int>, greater<int>> minHeap; // Right half

public:
    void addNum(int num) {
        maxHeap.push(num);
        minHeap.push(maxHeap.top());
        maxHeap.pop();
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }
    
    double findMedian() {
        return maxHeap.size() > minHeap.size() ? maxHeap.top() : (maxHeap.top() + minHeap.top()) / 2.0;
    }
};
```

### Top K Frequent Elements
```
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> count;
    for (int num : nums) count[num]++;
    priority_queue<pair<int, int>> pq;
    for (auto& p : count) pq.push({p.second, p.first});
    vector<int> result;
    for (int i = 0; i < k; i++) {
        result.push_back(pq.top().second);
        pq.pop();
    }
    return result;
}
```

### Word Ladder
```
int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
    unordered_set<string> wordSet(wordList.begin(), wordList.end());
    if (!wordSet.count(endWord)) return 0;
    queue<string> q;
    q.push(beginWord);
    int length = 1;
    while (!q.empty()) {
        int size = q.size();
        for (int i = 0; i < size; i++) {
            string word = q.front();
            q.pop();
            if (word == endWord) return length;
            wordSet.erase(word);
            for (int j = 0; j < word.size(); j++) {
                char c = word[j];
                for (int k = 'a'; k <= 'z'; k++) {
                    word[j] = k;
                    if (wordSet.count(word)) q.push(word);
                }
                word[j] = c;
            }
        }
        length++;
    }
    return 0;
}
```

### Trapping Rain Water
```
int trap(vector<int>& height) {
    int left = 0, right = height.size() - 1, leftMax = 0, rightMax = 0, water = 0;
    while (left < right) {
        if (height[left] < height[right]) {
            leftMax = max(leftMax, height[left]);
            water += leftMax - height[left];
            left++;
        } else {
            rightMax = max(rightMax, height[right]);
            water += rightMax - height[right];
            right--;
        }
    }
    return water;
}
```

### Preparation Tips
- Practice Regularly: Use platforms like LeetCode, HackerRank, and CodeSignal.
- Understand Fundamentals: Focus on time complexity, space complexity, and edge cases.
- Mock Interviews: Simulate real interview conditions with peers or using interview prep tools.


## 2. Coding (Ability to Write Logical & Maintainable Code)
Here, focus on writing clean, efficient, and well-structured code. Emphasize code readability, modularity, and proper use of data structures.

### LRU Cache Implementation
```
class LRUCache {
    int capacity;
    list<int> lru;
    unordered_map<int, pair<int, list<int>::iterator>> cache;

public:
    LRUCache(int capacity) : capacity(capacity) {}

    int get(int key) {
        if (cache.find(key) == cache.end()) return -1;
        lru.erase(cache[key].second);
        lru.push_front(key);
        cache[key].second = lru.begin();
        return cache[key].first;
    }

    void put(int key, int value) {
        if (cache.find(key) != cache.end()) {
            lru.erase(cache[key].second);
        } else if (lru.size() >= capacity) {
            cache.erase(lru.back());
            lru.pop_back();
        }
        lru.push_front(key);
        cache[key] = {value, lru.begin()};
    }
};
```

### Design a Stack with Increment Operation
```
class CustomStack {
    vector<int> stack;
    int maxSize;

public:
    CustomStack(int maxSize) : maxSize(maxSize) {}

    void push(int x) {
        if (stack.size() < maxSize) {
            stack.push_back(x);
        }
    }

    int pop() {
        if (stack.empty()) return -1;
        int val = stack.back();
        stack.pop_back();
        return val;
    }

    void increment(int k, int val) {
        for (int i = 0; i < min(k, (int)stack.size()); i++) {
            stack[i] += val;
        }
    }
};
```

### Flatten a Nested List Iterator
```
class NestedIterator {
    vector<int> list;
    int index = 0;

    void flattenList(const vector<NestedInteger>& nestedList) {
        for (const auto& ni : nestedList) {
            if (ni.isInteger()) {
                list.push_back(ni.getInteger());
            } else {
                flattenList(ni.getList());
            }
        }
    }

public:
    NestedIterator(vector<NestedInteger> &nestedList) {
        flattenList(nestedList);
    }

    int next() {
        return list[index++];
    }

    bool hasNext() {
        return index < list.size();
    }
};
```

### Design Hit Counter
```
class HitCounter {
    queue<pair<int, int>> hits;

public:
    void hit(int timestamp) {
        if (!hits.empty() && hits.back().first == timestamp) {
            hits.back().second++;
        } else {
            hits.push({timestamp, 1});
        }
    }

    int getHits(int timestamp) {
        while (!hits.empty() && hits.front().first <= timestamp - 300) {
            hits.pop();
        }
        int count = 0;
        for (auto& hit : hits) {
            count += hit.second;
        }
        return count;
    }
};
```

### Serialize and Deserialize Binary Tree
```
class Codec {
public:
    string serialize(TreeNode* root) {
        if (!root) return "null";
        return to_string(root->val) + "," + serialize(root->left) + "," + serialize(root->right);
    }

    TreeNode* deserialize(string data) {
        queue<string> q;
        string str;
        for (char c : data) {
            if (c == ',') {
                q.push(str);
                str = "";
            } else {
                str += c;
            }
        }
        if (!str.empty()) q.push(str);
        return deserializeHelper(q);
    }

private:
    TreeNode* deserializeHelper(queue<string>& q) {
        if (q.empty()) return nullptr;
        string str = q.front();
        q.pop();
        if (str == "null") return nullptr;
        TreeNode* root = new TreeNode(stoi(str));
        root->left = deserializeHelper(q);
        root->right = deserializeHelper(q);
        return root;
    }
};
```

### Design a Tic-Tac-Toe
```
class TicTacToe {
    vector<int> rows, cols;
    int diag1, diag2, n;

public:
    TicTacToe(int n) : n(n), rows(n, 0), cols(n, 0), diag1(0), diag2(0) {}

    int move(int row, int col, int player) {
        int val = (player == 1) ? 1 : -1;
        rows[row] += val;
        cols[col] += val;
        if (row == col) diag1 += val;
        if (row + col == n - 1) diag2 += val;

        if (abs(rows[row]) == n || abs(cols[col]) == n || abs(diag1) == n || abs(diag2) == n) {
            return player;
        }
        return 0;
    }
};
```

### Design a Phone Directory
```
class PhoneDirectory {
    queue<int> available;
    unordered_set<int> used;
    int maxNumbers;

public:
    PhoneDirectory(int maxNumbers) : maxNumbers(maxNumbers) {
        for (int i = 0; i < maxNumbers; i++) {
            available.push(i);
        }
    }

    int get() {
        if (available.empty()) return -1;
        int number = available.front();
        available.pop();
        used.insert(number);
        return number;
    }

    bool check(int number) {
        return used.find(number) == used.end();
    }

    void release(int number) {
        if (used.find(number) != used.end()) {
            used.erase(number);
            available.push(number);
        }
    }
};
```

### Design Circular Queue
```
class MyCircularQueue {
    vector<int> queue;
    int head, tail, size;

public:
    MyCircularQueue(int k) : queue(k), head(-1), tail(-1), size(0) {}

    bool enQueue(int value) {
        if (isFull()) return false;
        if (isEmpty()) head = 0;
        tail = (tail + 1) % queue.size();
        queue[tail] = value;
        size++;
        return true;
    }

    bool deQueue() {
        if (isEmpty()) return false;
        if (head == tail) {
            head = tail = -1;
        } else {
            head = (head + 1) % queue.size();
        }
        size--;
        return true;
    }

    int Front() {
        return isEmpty() ? -1 : queue[head];
    }

    int Rear() {
        return isEmpty() ? -1 : queue[tail];
    }

    bool isEmpty() {
        return size == 0;
    }

    bool isFull() {
        return size == queue.size();
    }
};
```

### Design a Key-Value Store
```
class KeyValueStore {
    unordered_map<string, string> store;
    unordered_map<string, unordered_set<string>> prefixMap;

public:
    void put(string key, string value) {
        store[key] = value;
        for (int i = 1; i <= key.size(); i++) {
            prefixMap[key.substr(0, i)].insert(key);
        }
    }

    string get(string key) {
        return store.count(key) ? store[key] : "";
    }

    vector<string> searchByPrefix(string prefix) {
        if (!prefixMap.count(prefix)) return {};
        return vector<string>(prefixMap[prefix].begin(), prefixMap[prefix].end());
    }
};
```

### Design Rate Limiter
```
class RateLimiter {
    unordered_map<string, queue<int>> userRequests;
    int limit, windowSize;

public:
    RateLimiter(int limit, int windowSize) : limit(limit), windowSize(windowSize) {}

    bool isAllowed(string userId, int timestamp) {
        if (userRequests[userId].size() < limit) {
            userRequests[userId].push(timestamp);
            return true;
        }

        while (!userRequests[userId].empty() && userRequests[userId].front() <= timestamp - windowSize) {
            userRequests[userId].pop();
        }

        if (userRequests[userId].size() < limit) {
            userRequests[userId].push(timestamp);
            return true;
        }

        return false;
    }
};
```

### Preparation Tips
- Code Readability: Use meaningful variable names and comments to describe the logic.
- Modular Code: Break down the problem into functions and classes.
- Edge Cases: Consider and handle edge cases thoroughly.

## 3. Coding (Problem Solving Focused Session)
This round emphasizes problem-solving skills and the ability to think through complex problems.

### Word Search
```
bool exist(vector<vector<char>>& board, string word) {
    int m = board.size(), n = board[0].size();
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (dfs(board, word, i, j, 0)) return true;
        }
    }
    return false;
}

bool dfs(vector<vector<char>>& board, string& word, int i, int j, int k) {
    if (k == word.size()) return true;
    if (i < 0 || i >= board.size() || j < 0 || j >= board[0].size() || board[i][j] != word[k]) return false;
    char tmp = board[i][j];
    board[i][j] = '#';
    bool found = dfs(board, word, i + 1, j, k + 1) ||
                 dfs(board, word, i - 1, j, k + 1) ||
                 dfs(board, word, i, j + 1, k + 1) ||
                 dfs(board, word, i, j - 1, k + 1);
    board[i][j] = tmp;
    return found;
}
```

### Course Schedule
```
bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
    vector<vector<int>> graph(numCourses);
    vector<int> inDegree(numCourses, 0);
    for (auto& pre : prerequisites) {
        graph[pre[1]].push_back(pre[0]);
        inDegree[pre[0]]++;
    }
    queue<int> q;
    for (int i = 0; i < numCourses; i++) {
        if (inDegree[i] == 0) q.push(i);
    }
    int count = 0;
    while (!q.empty()) {
        int course = q.front();
        q.pop();
        count++;
        for (int nextCourse : graph[course]) {
            if (--inDegree[nextCourse] == 0) q.push(nextCourse);
        }
    }
    return count == numCourses;
}
```

### Merge k Sorted Lists
```
ListNode* mergeKLists(vector<ListNode*>& lists) {
    auto comp = [](ListNode* a, ListNode* b) { return a->val > b->val; };
    priority_queue<ListNode*, vector<ListNode*>, decltype(comp)> pq(comp);
    for (auto list : lists) {
        if (list) pq.push(list);
    }
    ListNode* dummy = new ListNode(0), *tail = dummy;
    while (!pq.empty()) {
        ListNode* node = pq.top();
        pq.pop();
        tail->next = node;
        tail = node;
        if (node->next) pq.push(node->next);
    }
    return dummy->next;
}
```

### Clone Graph
```
Node* cloneGraph(Node* node) {
    if (!node) return nullptr;
    unordered_map<Node*, Node*> visited;
    return cloneGraphDFS(node, visited);
}

Node* cloneGraphDFS(Node* node, unordered_map<Node*, Node*>& visited) {
    if (visited.count(node)) return visited[node];
    Node* clone = new Node(node->val);
    visited[node] = clone;
    for (Node* neighbor : node->neighbors) {
        clone->neighbors.push_back(cloneGraphDFS(neighbor, visited));
    }
    return clone;
}
```

### Largest Rectangle in Histogram
```
int largestRectangleArea(vector<int>& heights) {
    stack<int> s;
    heights.push_back(0);
    int maxArea = 0;
    for (int i = 0; i < heights.size(); i++) {
        while (!s.empty() && heights[s.top()] > heights[i]) {
            int height = heights[s.top()];
            s.pop();
            int width = s.empty() ? i : i - s.top() - 1;
            maxArea = max(maxArea, height * width);
        }
        s.push(i);
    }
    return maxArea;
}
```

### Sliding Window Maximum
```
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;
    vector<int> result;
    for (int i = 0; i < nums.size(); i++) {
        if (!dq.empty() && dq.front() == i - k) dq.pop_front();
        while (!dq.empty() && nums[dq.back()] < nums[i]) dq.pop_back();
        dq.push_back(i);
        if (i >= k - 1) result.push_back(nums[dq.front()]);
    }
    return result;
}
```

### Implement Trie (Prefix Tree)
```
class Trie {
private:
    struct TrieNode {
        unordered_map<char, TrieNode*> children;
        bool isEndOfWord = false;
    };

    TrieNode* root;

public:
    Trie() {
        root = new TrieNode();
    }

    void insert(string word) {
        TrieNode* node = root;
        for (char c : word) {
            if (!node->children[c]) {
                node->children[c] = new TrieNode();
            }
            node = node->children[c];
        }
        node->isEndOfWord = true;
    }

    bool search(string word) {
        TrieNode* node = root;
        for (char c : word) {
            if (!node->children[c]) return false;
            node = node->children[c];
        }
        return node->isEndOfWord;
    }

    bool startsWith(string prefix) {
        TrieNode* node = root;
        for (char c : prefix) {
            if (!node->children[c]) return false;
            node = node->children[c];
        }
        return true;
    }
};
```

// Implement LRU Cache
```
class LRUCache {
    int capacity;
    list<pair<int, int>> cache;
    unordered_map<int, list<pair<int, int>>::iterator> map;

public:
    LRUCache(int capacity) : capacity(capacity) {}

    int get(int key) {
        if (map.find(key) == map.end()) return -1;
        cache.splice(cache.begin(), cache, map[key]);
        return map[key]->second;
    }

    void put(int key, int value) {
        if (map.find(key) != map.end()) {
            cache.splice(cache.begin(), cache, map[key]);
            map[key]->second = value;
        } else {
            if (cache.size() == capacity) {
                map.erase(cache.back().first);
                cache.pop_back();
            }
            cache.push_front({key, value});
            map[key] = cache.begin();
        }
    }
};
```

### Design Hit Counter
```
class HitCounter {
    queue<pair<int, int>> hits;

public:
    void hit(int timestamp) {
        if (!hits.empty() && hits.back().first == timestamp) {
            hits.back().second++;
        } else {
            hits.push({timestamp, 1});
        }
    }

    int getHits(int timestamp) {
        while (!hits.empty() && hits.front().first <= timestamp - 300) {
            hits.pop();
        }
        int count = 0;
        for (auto& hit : hits) {
            count += hit.second;
        }
        return count;
    }
};
```

## Median of Two Sorted Arrays
```
double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
    int m = nums1.size(), n = nums2.size();
    if (m > n) return findMedianSortedArrays(nums2, nums1);
    int low = 0, high = m, halfLen = (m + n + 1) / 2;
    while (low <= high) {
        int i = (low + high) / 2;
        int j = halfLen - i;
        if (i < m && nums1[i] < nums2[j - 1]) {
            low = i + 1;
        } else if (i > 0 && nums1[i - 1] > nums2[j]) {
            high = i - 1;
        } else {
            int maxLeft = 0;
            if (i == 0) maxLeft = nums2[j - 1];
            else if (j == 0) maxLeft = nums1[i - 1];
            else maxLeft = max(nums1[i - 1], nums2[j - 1]);
            if ((m + n) % 2 == 1) return maxLeft;
            int minRight = 0;
            if (i == m) minRight = nums2[j];
            else if (j == n) minRight = nums1[i];
            else minRight = min(nums1[i], nums2[j]);
            return (maxLeft + minRight) / 2.0;
        }
    }
    return 0.0;
}
```

### Preparation Tips
- Optimize: Aim for optimal solutions, not just correct ones.
- Dry Run: Walk through your solution with sample inputs.
- Edge Cases: Consider edge cases and handle them.

## 4. System Design
This round tests your ability to design large-scale distributed systems.

1. Design a URL Shortener
    Key Points: Database schema, generating unique short URLs, handling collisions, scaling, and analytics.
2. Design a Chat System
    Key Points: Real-time messaging, user presence, message storage, and retrieval.
3. Design a Rate Limiter
    Key Points: Throttling, sliding window algorithm, distributed environment.
4. Design a Social Media Feed
    Key Points: Feed generation, ranking algorithms, caching, handling high read/write throughput.
5. Design a File Storage Service (like Dropbox)
    Key Points: File metadata storage, versioning, chunking large files, sync across devices.
6. Design an Online Bookstore
    Key Points: Inventory management, search, recommendation system, payment processing.
7. Design a Notification System
    Key Points: Push notifications, email, SMS, delivery guarantees, rate limiting.
8. Design a Search Autocomplete System
    Key Points: Trie data structure, caching, ranking results.
9. Design a Ride Sharing Service
    Key Points: Matching algorithms, handling surge pricing, route optimization.
10. Design a Video Streaming Service
    Key Points: Video encoding, CDN, handling high concurrency, recommendations.

### Preparation Tips
- High-Level Architecture: Start with a high-level architecture and dive into details.
- Trade-offs: Discuss trade-offs and justify your design choices.
- Scalability: Consider scalability and performance implications.
- Diagrams: Use diagrams to illustrate your design.

## 5. Behavioral Interview
Amazon's behavioral interviews focus on their Leadership Principles. Prepare examples from your experience that demonstrate these principles.

#### Sample Questions
1. Tell me about a time you demonstrated leadership.
2. Describe a time you took a calculated risk.
3. How do you handle a disagreement with a team member?
4. Give an example of a time you delivered results under tight deadlines.
5. Describe a situation where you showed customer obsession.
6. Tell me about a time you failed and how you handled it.
7. How do you prioritize tasks when you have multiple deadlines?
8. Describe a time you innovated on a project.
9. Give an example of a time you had to earn trust from a stakeholder.
10. Describe a situation where you went above and beyond for a customer.

### Preparation Tips
- STAR Method: Use the Situation, Task, Action, Result method to structure your responses.
- Leadership Principles: Familiarize yourself with Amazon's Leadership Principles and align your examples with them.
- Practice: Practice your responses to ensure clarity and confidence.
