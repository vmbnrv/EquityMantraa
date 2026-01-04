# Take/Skip (Choose/Not Choose) Pattern Problems Reference

This document summarizes classic problems that use the take/skip recursion pattern, with intuition and code for each.

---

## 1. Subsets (Power Set)
**Problem:** Return all possible subsets of a set of unique integers.

**Intuition:** At each index, either include (take) or exclude (skip) the current element.

**Code:**
```cpp
void solve(vector<int>& nums, int idx, vector<int>& path, vector<vector<int>>& res) {
    if (idx == nums.size()) {
        res.push_back(path);
        return;
    }
    // Take
    path.push_back(nums[idx]);
    solve(nums, idx + 1, path, res);
    path.pop_back();
    // Skip
    solve(nums, idx + 1, path, res);
}

vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> path;
    solve(nums, 0, path, res);
    return res;
}
```

**Memoization Version:**
```cpp
// For subsets, memoization is not typically used because all paths are unique, but you can memoize on (idx, path) if needed for variants with duplicate elements.
```

---

## 2. Combination Sum
**Problem:** Find all unique combinations of candidates where chosen numbers sum to target. Each number may be used unlimited times.

**Intuition:** At each index, take (use the coin, stay at idx) or skip (move to next idx).

**Code:**
```cpp
void solve(vector<int>& candidates, int idx, int target, vector<int>& path, vector<vector<int>>& res) {
    if (target == 0) {
        res.push_back(path);
        return;
    }
    if (idx == candidates.size() || target < 0) return;
    // Take
    path.push_back(candidates[idx]);
    solve(candidates, idx, target - candidates[idx], path, res);
    path.pop_back();
    // Skip
    solve(candidates, idx + 1, target, path, res);
}

vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    vector<vector<int>> res;
    vector<int> path;
    solve(candidates, 0, target, path, res);
    return res;
}
```

**Memoization Version:**
```cpp
// For combination sum, memoization is not standard since all paths are unique, but you can memoize on (idx, target) for variants with duplicate elements.
```

---

## 3. 0/1 Knapsack
**Problem:** Maximize value in a knapsack of capacity W, each item can be used at most once.

**Intuition:** At each item, take (if it fits) or skip.

**Code:**
```cpp
int solve(vector<int>& weights, vector<int>& values, int idx, int capacity) {
    if (idx == weights.size() || capacity == 0) return 0;
    int skip = solve(weights, values, idx + 1, capacity);
    int take = 0;
    if (weights[idx] <= capacity) {
        take = values[idx] + solve(weights, values, idx + 1, capacity - weights[idx]);
    }
    return max(take, skip);
}

int knapsack(vector<int>& weights, vector<int>& values, int capacity) {
    return solve(weights, values, 0, capacity);
}
```

**Memoization Version:**
```cpp
int solve(vector<int>& weights, vector<int>& values, int idx, int capacity, vector<vector<int>>& memo) {
    if (idx == weights.size() || capacity == 0) return 0;
    if (memo[idx][capacity] != -1) return memo[idx][capacity];
    int skip = solve(weights, values, idx + 1, capacity, memo);
    int take = 0;
    if (weights[idx] <= capacity) {
        take = values[idx] + solve(weights, values, idx + 1, capacity - weights[idx], memo);
    }
    return memo[idx][capacity] = max(take, skip);
}
```

---

## 4. Partition Equal Subset Sum (LeetCode 416)
**Problem:** Can you partition nums into two subsets with equal sum?

**Intuition:** Try to find a subset that sums to total_sum/2. At each index, take (subtract from target) or skip.

**Code:**
```cpp
bool solve(vector<int>& nums, int idx, int target, vector<vector<int>>& memo) {
    if (target == 0) return true;
    if (idx == nums.size() || target < 0) return false;
    if (memo[idx][target] != -1) return memo[idx][target];
    if (solve(nums, idx + 1, target - nums[idx], memo)) return memo[idx][target] = true;
    if (solve(nums, idx + 1, target, memo)) return memo[idx][target] = true;
    return memo[idx][target] = false;
}

bool canPartition(vector<int>& nums) {
    int sum = accumulate(nums.begin(), nums.end(), 0);
    if (sum % 2 != 0) return false;
    int target = sum / 2;
    vector<vector<int>> memo(nums.size(), vector<int>(target + 1, -1));
    return solve(nums, 0, target, memo);
}
```

// Already uses memoization above.
```

---

## 5. Longest Increasing Subsequence (LIS)
**Problem:** Return the length of the longest strictly increasing subsequence.

**Intuition:** At each index, take (if nums[currIdx] > nums[prevIdx]) or skip.

**Code:**
```cpp
int solve(vector<int>& nums, int currIdx, int prevIdx, vector<vector<int>>& memo) {
    if (currIdx == nums.size()) return 0;
    if (memo[currIdx][prevIdx + 1] != -1) return memo[currIdx][prevIdx + 1];
    int take = 0;
    if (prevIdx == -1 || nums[currIdx] > nums[prevIdx]) {
        take = 1 + solve(nums, currIdx + 1, currIdx, memo);
    }
    int skip = solve(nums, currIdx + 1, prevIdx, memo);
    return memo[currIdx][prevIdx + 1] = max(take, skip);
}

int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<vector<int>> memo(n, vector<int>(n + 1, -1));
    return solve(nums, 0, -1, memo);
}
```

// Already uses memoization above.
```

---

## 6. Palindrome Partitioning (LeetCode 131)
**Problem:** Partition s so every substring is a palindrome. Return all possible partitions.

**Intuition:** For each start, try all ends. If s[start...end] is a palindrome, take it and recurse.

**Code:**
```cpp
bool isPalindrome(const string& s, int left, int right) {
    while (left < right) {
        if (s[left++] != s[right--]) return false;
    }
    return true;
}

void solve(const string& s, int start, vector<string>& path, vector<vector<string>>& res) {
    if (start == s.size()) {
        res.push_back(path);
        return;
    }
    for (int end = start; end < s.size(); ++end) {
        if (isPalindrome(s, start, end)) {
            path.push_back(s.substr(start, end - start + 1));
            solve(s, end + 1, path, res);
            path.pop_back();
        }
    }
}

vector<vector<string>> partition(string s) {
    vector<vector<string>> res;
    vector<string> path;
    solve(s, 0, path, res);
    return res;
}
```

**Memoization Version:**
```cpp
// You can memoize palindrome checks with a 2D table, and/or memoize on start index for the solve function for optimization.
```

---

## 7. Coin Change (LeetCode 322)
**Problem:** Fewest coins to make up amount. Return -1 if not possible.

**Intuition:** At each coin, take (use coin, stay at idx) or skip (move to next coin).

**Code (1D DP):**
```cpp
int solve(vector<int>& coins, int amount, vector<int>& dp) {
    if (amount == 0) return 0;
    if (amount < 0) return INT_MAX / 2;
    if (dp[amount] != -1) return dp[amount];
    int res = INT_MAX / 2;
    for (int coin : coins) {
        res = min(res, 1 + solve(coins, amount - coin, dp));
    }
    return dp[amount] = res;
}

int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount + 1, -1);
    int result = solve(coins, amount, dp);
    return result >= INT_MAX / 2 ? -1 : result;
}
```

// Already uses memoization above.
```

---

## 8. Word Break (LeetCode 139)
**Problem:** Can s be segmented into a sequence of dictionary words?

**Intuition:** For each start, try all ends. If s[start...end] is in dict, take and recurse from end+1.

**Code:**
```cpp
bool solve(const string& s, int start, const unordered_set<string>& dict, vector<int>& memo) {
    if (start == s.size()) return true;
    if (memo[start] != -1) return memo[start];
    for (int end = start; end < s.size(); ++end) {
        string word = s.substr(start, end - start + 1);
        if (dict.count(word) && solve(s, end + 1, dict, memo)) {
            return memo[start] = true;
        }
    }
    return memo[start] = false;
}

bool wordBreak(string s, vector<string>& wordDict) {
    unordered_set<string> dict(wordDict.begin(), wordDict.end());
    vector<int> memo(s.size(), -1);
    return solve(s, 0, dict, memo);
}
```

// Already uses memoization above.
```

---

## 9. Subset Sum
**Problem:** Is there a subset of nums that sums to target?

**Intuition:** At each index, take (subtract from target) or skip.

**Code:**
```cpp
bool solve(vector<int>& nums, int idx, int target, vector<vector<int>>& memo) {
    if (target == 0) return true;
    if (idx == nums.size() || target < 0) return false;
    if (memo[idx][target] != -1) return memo[idx][target];
    if (solve(nums, idx + 1, target - nums[idx], memo)) return memo[idx][target] = true;
    if (solve(nums, idx + 1, target, memo)) return memo[idx][target] = true;
    return memo[idx][target] = false;
}

bool subsetSum(vector<int>& nums, int target) {
    vector<vector<int>> memo(nums.size(), vector<int>(target + 1, -1));
    return solve(nums, 0, target, memo);
}
```

// Already uses memoization above.
```

---

## How to Identify Take/Skip Problems
- Problem asks for all subsets, combinations, or partitions.
- At each step, you can include (take) or exclude (skip) an element.
- You need to explore all possible ways, counts, or check for existence.
- Classic keywords: "all subsets", "all combinations", "partition", "can you split", "ways to choose".

---

**Tip:** If you can answer “yes” to: “At each step, can I take or skip the current element?”—the take/skip pattern is likely a good fit!
