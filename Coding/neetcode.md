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
  
  ### Container with Most Water
  #### You are given an integer array `heights` where `heights[i]` represents the height of the ith bar. You may choose any two bars to form a container. Return the maximum amount of water a container can store.
  ```cpp
    int maxArea(vector<int>& heights) {
        int i = 0;
        int j = heights.size()-1;
        int ans = 0;
        for(;i<j;) {
            int area = min(heights[i], heights[j]) * (j-i);
            ans = max(ans, area);
            if(heights[i] < heights[j]) {
                i++;
            } else {
                j--;
            }
        }
        return ans;
    }
  ```
### Trapping Rain Water
#### You are given an array non-negative integers `heights` which represent an elevation map. Each value `heights[i]` represents the height of a bar, which has a width of 1.
```cpp
int trap(vector<int>& height) {
    int left = 0, right = height.size() - 1;
    int leftMax = 0, rightMax = 0, water = 0;
    
    while(left < right) {
        if(height[left] < height[right]) {
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
### 3 Sum 
#### Find 3 integers in an array that add up to 0.
```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        sort(nums.begin(), nums.end());
        for(int i = 0; i < nums.size();i++) {
            // Ignore all the processed duplicates
            while(i > 0 && nums[i] == nums[i-1]) i++;

            int l = i + 1;
            int r = nums.size() - 1;
            while(l < r) {
                int sum = nums[i] + nums[l] + nums[r];
                if(sum < 0) {
                    l++;
                } else if(sum > 0) {
                    r--;
                } else {
                    vector<int> triplet{nums[i], nums[l], nums[r]};
                    res.push_back(triplet);
                    while(l < r && nums[l] == triplet[1]) l++;
                    while(l < r && nums[r] == triplet[2]) r--;
                }
            }
        }

        return res;
    }
};

```

### Is Palindrome
#### Given a string, return `true` if it is a palindrome. Return `false` otherwise.
```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        for(int i = 0, j = s.length()-1; i < j; ) {
            if (!isalnum(s[i])) {
                i++;
            } else if (!isalnum(s[j])) {
                j--;
            } else if (tolower(s[i]) != tolower(s[j])) {
                return false;
            } else {
                i++;
                j--;
            }
        }
        return true;
    }
};

```
</details>

<details>
  <summary> Sliding Window </summary>
  
### Longest Substring without duplicates
```cpp
    int lengthOfLongestSubstring(string s) {
        int len = 0;
        vector<bool> hash(128, false);
        int l = 0;
        for(int r = 0;r < s.length();r++){
            while(hash[s[r]]) {
                hash[s[l]] = false;
                l++;
            }
            hash[s[r]] = true;
            len = max(len, r - l + 1);
        }
        return len;
    }
```
### Longest Repeating Substring With Replacement
```cpp
    int characterReplacement(string s, int k) {
        vector<int> count(26, 0);
        int res = 0;
        int maxCount = 0;
        int l = 0;
        int r = 0;
        while(r < s.size()){
            int cur = s[r] - 'A';
            count[cur]++;
            maxCount = max(maxCount, count[cur]);
            
            /* Move the window if the current window size exceeds
               maximum duplicate characters with k replacements
            */
            if(r - l + 1 > k + maxCount) {
                int leftCharIndex = s[l] - 'A';
                count[leftCharIndex]--;
                l++;
            }
            res = max(res, r - l + 1);
            r++;
        }
        return res;
    }
```
### Permutated String inclusion
#### You are given two strings s1 and s2. Return true if s2 contains a permutation of s1, or false otherwise. That means if a permutation of s1 exists as a substring of s2, then return true.
```
Input Examples:
s1 = "abc", s2 = "lecabee" => true
s1 = "abc", s2 = "lecaabee" => false
```
```cpp
    bool checkInclusion(string s1, string s2) {
        vector<int> s1Freq(26,0), s2Freq(26,0);
        int n = s1.length();
        int m = s2.length();
        
        if(n > m) return false;

        for(int i = 0; i < n; i++) {
            s1Freq[s1[i] - 'a']++;
            s2Freq[s2[i] - 'a']++;
        }

        if(s1Freq == s2Freq) return true; // s2 itself is a permutation of s1
        
        // Use sliding window approach
        for(int i = n; i < m;i++) {
            s2Freq[s2[i] - 'a']++;  // Include the next right character into the window
            s2Freq[s2[i-n] - 'a']--; // Remove the first character in the window;
            if(s1Freq == s2Freq) return true;
        }

        return false;
    }
    // O(m), as comparing two vectors is O(26) which takes constant time.
```
### Minimum Window with Characters
#### Given two strings `s` and `t`, return the shortest substring of `s` such that every character in `t`, including duplicates, is present in the substring. Return `""` if there isn't one.
```
Input Examples:
s = "OUZODYXAZV", t = "XYZ" => "YXAZ"
s = "x", t = "xy" => ""
```
```cpp
string minWindow(string s, string t) {
        if(s.empty() || t.empty()) return "";

        unordered_map<char, int> t_count;
        for(auto c : t) {
            t_count[c]++;
        }

        int formed = 0, required = t_count.size();
        unordered_map<char, int> windowCount;

        int left = 0, right = 0;
        int minLen = INT_MAX, minLeft = 0, minRight = 0;
        
        while( right < s.size() ) {
            char c = s[right];
            windowCount[c]++;

            if(t_count.find(c) != t_count.end() && windowCount[c] == t_count[c]) {
                formed++;
            }

            while(left <= right && formed == required) {
                c = s[left];

                if(right - left + 1 < minLen) {
                    minLen = right - left + 1;
                    minLeft = left;
                    minRight = right;
                }

                windowCount[c]--;
                if(t_count.find(c) != t_count.end() && windowCount[c] < t_count[c]) {
                    formed--;
                }
                left++;
            }
            right++;
        }
        return minLen == INT_MAX ? "" : s.substr(minLeft, minLen);
    }
```
### Sliding Window Maximum
Given an array and `k`, return the maximum element in every window of size k within the array.
```
Input: nums = [1,2,1,0,4,2,6], k = 3
Output: [2,2,4,4,6]
```
```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> res;
        deque<int> dq; // store indices
        int val = INT_MIN;
        for(int i = 0; i < nums.size();i++) {
            // Pop front element every time the dq slides
            if(!dq.empty() && dq.front() == i-k) {
                dq.pop_front();
            }
            // Ensure that dq.front() always contains the largest value
            while(!dq.empty() && nums[dq.back()] < nums[i]) {
                dq.pop_back();
            }
            dq.push_back(i);

            if(i >= k - 1) {
                res.push_back(nums[dq.front()]);
            }
        }
        return res;
    }
```
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
