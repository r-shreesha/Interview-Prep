<details>
  <summary> Arrays & Hashing </summary>

### Duplicate Integer
#### Given an integer array nums, return true if any value appears more than once in the array, otherwise return false.
```cpp
class Solution {
public:
    bool hasDuplicate(vector<int>& nums) {
        unordered_set<int> hash;
        for(auto n: nums) {
            if(hash.count(n)) {
                return true;
            }
            hash.insert(n);
        }
        return false;
    }
};
```

### Is Anagram
#### Given two strings s and t, return true if the two strings are anagrams of each other, otherwise return false.
An anagram is a string that contains the exact same characters as another string, but the order of the characters can be different.
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        unordered_map<char, int> hash;
        for(auto c: s) {
            hash[c]++;
        }
    
        for(auto c: t) {
            if(hash.count(c) == 0) {
                return false;
            }
            if(--hash[c] == 0) {
                hash.erase(c);
            }
        }
        return hash.size() == 0;
    }
};
```

### Top K Elements in List
#### Given an integer array nums and an integer k, return the k most frequent elements within the array.
You may return the output in any order.
```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        priority_queue<pair<int,int>> maxH; // <Frequency, num>
        unordered_map<int,int> count; // num -> frequency
        for(auto n: nums) {
            count[n] += 1;
        }
        for(auto ent: count) {
            maxH.push({ent.second, ent.first});
        }

        vector<int> res;
        while(k > 0 && !maxH.empty()) {
            auto v = maxH.top();
            maxH.pop();
            res.push_back(v.second);
            k--;
        }
        return res;
    }
};
```

### String Encode and Decode
#### Design an algorithm to encode a list of strings to a single string. The encoded string is then decoded back to the original list of strings.
```cpp
class Solution {
public:

    string encode(vector<string>& strs) {
        string result = "";
            
        for (int i = 0; i < strs.size(); i++) {
            string str = strs[i];
            result += to_string(str.size()) + "#" + str; // "[length]#[string]"
        }
        
        //4#neet4#code4#love3#you
        return result;
    }

    vector<string> decode(string s) {
        vector<string> result;
        int i = 0;
        while(i < s.size()) {
            int j = i;
            // Capture string length
            while(s[j] != '#') {
                j++;
            }
            int len = stoi(s.substr(i, j-i));
            string str = s.substr(j+1, len);
            result.push_back(str);
            i = j + 1 + len;
        }
        return result;
    }
};
```

### Products of Array Discluding Self
#### Given an integer array nums, return an array output where output[i] is the product of all the elements of nums except nums[i].
Each product is guaranteed to fit in a 32-bit integer.
Follow-up: Could you solve it in O(n) time without using the division operation?
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> res(n, 1);

        // 1,2,3,4
        int prefix = 1;
        for(int i = 0; i < n; i++) {
            res[i] *= prefix;
            prefix *= nums[i];
        }
        // res[i] at the end of each iteration => 1, 1, 2, 6
        // prefix at the end of each iteration => 1, 2, 6, 24

        int postfix = 1;
        for(int i = n-1; i >= 0; i--) {
            res[i] *= postfix;
            postfix *= nums[i];
        }
        // res[i] at the end of each iteration(back to front) => 6, 8, 12, 24
        // postfix at the end of each iteration               => 4, 12, 24, 24
        
        return res; // 24, 12, 8, 6
    }
};
```

### Longest Consecutive Sequence
#### Given an array of integers nums, return the length of the longest consecutive sequence of elements.
A consecutive sequence is a sequence of elements in which each element is exactly 1 greater than the previous element.
You must write an algorithm that runs in O(n) time.

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> hash(nums.begin(), nums.end());
        int ans = 0;
        int length = 0;
        for(int i = 0; i < nums.size();i++) {
            if(hash.count(nums[i]-1) == 0) {
                int length = 1;
                while(hash.count(nums[i] + length)) length++;
                ans = max(ans, length);
            }
        }
        return ans;
    }
};
```
</details>
<details>
  <summary> Two Pointers </summary>
</details>

<details>
  <summary> Sliding Window </summary>
</details>

<details>
  <summary> Stack </summary>
</details>

<details>
  <summary> Trees </summary>
</details>

<details>
  <summary> Heap / Priority Queue </summary>
</details>

<details>
  <summary> Graphs </summary>
</details>

<details>
  <summary> Tries </summary>
</details>
<details>
  <summary> 1-D Dynamic Programming </summary>
</details>

<details>
  <summary> 2-D Dynamic Programming </summary>
</details>

<details>
  <summary> Greedy </summary>
</details>

<details>
  <summary> Intervals </summary>
</details>
