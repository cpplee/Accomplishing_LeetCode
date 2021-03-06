## The_way_of_accomplishing_LeetCode.
The way is not so far.It has 337 problems until 2016-03-15.
Go on!my friends.

#LeetCode 通关之路
@(E ACMer)

[toc]

by jibancanyang
***
##1.Two Sum(水)
**分析**:数学里的$\binom{2}{n}$.
**code**:
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for (int i = 0; i < nums.size(); i++) {
            for (int j = i + 1; j < nums.size(); j++) {
                if (nums[i] + nums[j] == target) {
                    vector<int> ret;
                    ret.push_back(i);
                    ret.push_back(j);
                    return ret;
                }
            }
        }
    }
};
```
***
##2.Add Two Numbers(水)
**分析**大数加法,链表实现.
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* ret = new ListNode(0);
        ListNode* cur = ret;
        int op = 0;
        bool c1 = true, c2 = true;
        while (true) {
            int a = 0, b = 0;
            if (c1) {
                a = l1 -> val;
                if (l1 -> next == NULL) c1 = false;
            }
            if (c2) {
                b = l2 -> val;
                if (l2 -> next == NULL) c2 = false;
            }
            cur -> val = (op + a + b) % 10;
            if (op + a + b > 9) op = 1;
            else op = 0;
            cur -> next = new ListNode(0);
            
            if (!c1 && !c2) {
                if (op) {
                    cur = cur -> next;
                    cur -> val = op;
                } else cur -> next = NULL;
                return ret;
            }
            cur = cur -> next;
            if (l1 -> next != NULL) l1 = l1 -> next;
            if (l2 -> next != NULL) l2 = l2 -> next;
        }
    }
};
```
***
##3.Longest Substring Without Repeating Characters(尺取法(双指针))
**题意**:求字符串最长不重复字符子序列的长度.
**分析**:
> **暴力**:最直接的方法就是通过枚举所有的头和尾把所有的子串找出来,然后判断这个子串是不是满足其中字符全部不重复.这样的时间复杂度是$O(n ^ 3)$,显然效率极低.
> **尺取法(双指针)** :维护一个双指针,保证头尾之间的序列一定是不重复的,用一个letters表来记录是否重复.不熟悉尺取法(又名双指针)的话会比较困难.该算法时间复杂度$O(n)$,是最佳时间复杂度.

**尺取法的代码**:
```cpp

class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        bool letters[260];
        memset(letters, 0, sizeof(letters));
        int head = 0, tail = 0, maxlen = 0;
        letters[s[head]] = true;
        while (tail < s.size()) {
            if (head < tail && letters[s[tail] ]) {
                letters[s[head++]] = false;
                while (s[head - 1] != s[tail]) letters[s[head++]] = false;
            }
            letters[s[tail]] = true;
            maxlen = max(tail - head + 1, maxlen);
            tail++;
        }
        return maxlen;
    }
};
```
***
##4.Median of Two Sorted Arrays(递归截断)
**题意**:给两个已经排序的数组,求它们合并后的中位数,要求复杂度是$O(log(n + m))$.
**分析**:
- 首先很容易想到利用归并排序合并两个数组的方法来合并这两个有序数组,时间复杂度是$O(n)$
- 然后继续想可以以第一个数组为基准,来进行二分.一旦第一个数组中的元素选定,由于中位数就是中间的数字,那么第二个数组中数的对应位置也就确定了.二分第一个数组的位置,然后判断第一个第二个数组的选择部分是否满足条件.这样实现了一晚上,失败..细节太多.
- 然后就是log(n + m) 的正确解答.利用了一个重要的性质.**如果我们要在两个有序数组中选择第$k$个数,那么取第一个数组的第$k / 2$个数,和第二个数组的第$k - k / 2$个数,如果第一个数组的$k / a$个数小于第二个数组的$k - k/ a$个数,那么第一个数组的前$k / 2$个数中一定没有目标中位数,直接截断,第二个数组小的话也同理**这样就是形成了一个每次让其中一个数组长度截断一半的递归...

> 注意的是一定要保证思路完全清晰了再开始写代码,然后尽量通读代码查错而不是靠单步调试...
> 思考过程就是列出所有可能具有的性质,然后来尽量让这些性质交织着向正确答案靠近.

**code**:
```

class Solution {
public:
    double doit(int a, int b, int t, vector<int> &v1, vector<int> &v2) {
        int ka = min((int)v1.size() - a, t / 2), kb = t - ka;
        //The first is always shorter.
        if (v1.size() - a > v2.size() - b) return doit(b, a, t, v2, v1);
        if (v1.size() == a) return v2[b + t - 1];
        
        //When only choose one number.
        if (t == 1) return double(min(v1[a], v2[b]));
        
        if (v1[a + ka - 1] == v2[b + kb - 1]) return double(v1[a + ka - 1]);
        else if (v1[a + ka - 1] < v2[b + kb - 1]) return doit(a + ka, b, t - ka, v1, v2);
        else return doit(a, b + kb, t - kb, v1, v2);
    }
    
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        if ((nums1.size() + nums2.size()) % 2) return doit(0, 0, (int)(nums1.size() + nums2.size() + 1) / 2, nums1, nums2);
        else return (doit(0, 0, (int)(nums1.size() + nums2.size() + 1) / 2, nums1, nums2) +
            doit(0, 0, (int)(nums1.size() + nums2.size() + 2) / 2, nums1, nums2)) / 2;
    }
};
```
***
##5.Longest Palindromic Substring(最长回文子串)
**题意**:求字符串的最长回问子串.
**分析**:
- 首先很容易想到暴力的方法,把回文子串分成奇数长度和偶数长度两种情况,把每个字符当做回文串的中心,然后求以它为奇数串中心(或者偶数串的左中心),求它为中心的最长回文串的长度.这样复杂度是$O(n ^2)$,LeetCode也能AC....

**暴力的代码:**
```
class Solution {
public:
    string longestPalindrome(string s) {
        int retodd = 0, reteven = 0, odd = 0, even = 0;
        for (int i = 0; i < s.size(); i++) {
            for (int l = i, r = i; l >= 0 && r < s.size(); l--, r++) {
                if (s[l] != s[r]) break;
                if (r - l + 1 > retodd) {
                    retodd = r - l + 1;
                    odd = i;
                } 
            }
            
            for (int l = i, r = i + 1; l >= 0 && r < s.size(); l--, r++) {
                if (s[l] != s[r]) break;
            
                if (r - l + 1 > reteven) {
                    reteven = r - l + 1;
                    even = i;
                } 
            }
        }
        if (retodd > reteven) {
            return s.substr(odd - retodd / 2, retodd);
        } else {
            return s.substr(even - reteven / 2 + 1, reteven);
        }
    }
};
```
**Manacher算法**:强悍的马拉车算法,时间复杂度$O(n)$...
```cpp
class Solution {
public:
    string preProcess(string s)
    {
        int n = s.length();
        if (n == 0) return "^$";
        string ret = "^";
        for (int i = 0; i < n; i++)
            ret += "#" + s.substr(i, 1);
    
        ret += "#$";
        return ret;
    }
    
    string longestpalindrome(string s)
    {
        string T = preProcess(s);
        int n = T.length();
        int* P = new int[n];
        int C = 0, R = 0;
        for (int i = 1; i < n - 1; i++) {
            int i_mirror = 2 * C - i; // 等于i' = C - (i-C)
    
        P[i] = (R > i) ? min(R-i, P[i_mirror]) : 0;
    
        // 尝试扩展中心为i的回文
        while (T[i + 1 + P[i]] == T[i - 1 - P[i]])
            P[i]++;
    
        // 如果中心为i的回文超越了R，根据已扩展的回文来调整中心
        if (i + P[i] > R) {
            C = i;
            R = i + P[i];
            }
        }
    
        // 找出P中的最大值
        int maxLen = 0;
        int centerIndex = 0;
        for (int i = 1; i < n-1; i++) {
            if (P[i] > maxLen) {
                maxLen = P[i];
                centerIndex = i;
            }
        }
        delete[] P;
    
        return s.substr((centerIndex - 1 - maxLen)/2, maxLen);
    }
    string longestPalindrome(string s) {
        return longestpalindrome(s);
    }
};
```

***
6.ZigZag Conversion(模拟)
分析:把一个Z字型的字符,按行输出,模拟一下就好...复杂度$O(n)$
```cpp
class Solution {
public:
    string convert(string s, int numRows) {
        if (numRows == 1) return s;
        int key = 2 * numRows - 2;
        string ans[10000];
        for (int i = 0; i < s.size(); i += key) {
            int l = i, k = i + key - 1;
            ans[l % key] += s[l];
            l++;
            while (l < k) {
                if (l < s.size()) ans[l % key] += s[l];
                if (k < s.size()) ans[l % key] += s[k];
                l++, k--;
            }
            if (l < s.size()) ans[l % key] += s[l];
        }
        string ret;
        for (int i = 0; i < key; i++) {
             if (!ans[i].empty()) ret += ans[i];
        }
        return ret;
    }
};
```
***
##7.Reverse Integer(模拟)
**题意**:翻转一个int型数,注意坑点是数据范围超过会出现问题.总是很草率的提交,写完了得检查一下逻辑,争取一次AC率高一些啊...
> **一些语言特性**:
> `#include <climits>`库里面有一些表示数据范围的常量:
> `INT_MAX` `INT_MIN` `LONG_MAX` `CHAR_MAX` ....诸如此类.

**本题代码**:
```
#include <climits>
class Solution {
public:
    long long reverse(int x) {
        string str = to_string(x);
        for (int i = (str[0] == '-' ? 1 : 0), j = (int)str.size() - 1; i < j; i++, j--) {
            swap(str[i], str[j]);
        }
        long long temp = 0;
//        cout << str << endl;
        for (int i = (str[0] == '-' ? 1 : 0); i < str.size(); i++) {
//            cout << temp << endl;
            temp = temp * 10 + str[i] - '0';
        }
        if (temp > INT_MAX) return 0;
        return str[0] == '-' ? -temp : temp;
    }
};
```

***

##8. String to Integer (atoi)(C++)
可以说是一道恶心的模拟题,但是恰好C++库里有可以直接拿来用的stringtoint的标准函数.
```
class Solution {
public:
    int myAtoi(string str) {
        if (str == "") return 0;
        istringstream ret(str); //定义一个输入字符串流
        int i;
        ret >> i; //把一个输入流中的内容输出为整型
        return i;
    }
};
```
***
##9.Palindrome Number(模拟)
**题意**:要求不用额外空间判断是否是回文数.
这里我用了一个int变量,根据(/10, %10)组合操作来取它所有的数.
```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0 || (x != 0 && x % 10 == 0)) return false;
        int y = 0;
        while (y < x) {
            y = y * 10 + x % 10;
            x /= 10;
        }
        return x == y || x == y / 10;
    }
};


```
##62. Unique Paths(dp)
**题意**:从格子的左上角走到右下角有多少种走法,只能往右或者往下走.
**分析**:直接dp即可.这里主要注意当dp写成递推的时候,更新顺序的选择时候道路方向相关的.
```
class Solution {
public:
    int uniquePaths(int m, int n) {
        int dp[111][111];
        memset(dp, 0, sizeof(dp));
        dp[m][n] = 1;
        
        for (int i = m ; i > 0; i--) {
            for (int j = n; j > 0; j--) {
                if (j + 1 <= n) dp[i][j] += dp[i][j + 1];
                if (i + 1 <= m) dp[i][j] += dp[i + 1][j];
            }
        }
        return dp[1][1];
    }
};
```
***
##63. Unique Paths II(dp)
类似上题而已...
```
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int dp[111][111], m = obstacleGrid.size(), n = obstacleGrid[0].size();
        memset(dp, 0, sizeof(dp));
        dp[m][n] = 1;
        
        for (int i = m ; i > 0; i--) {
            for (int j = n; j > 0; j--) {
                if (j + 1 <= n && !obstacleGrid[i - 1][j - 1] ) dp[i][j] += dp[i][j + 1];
                if (i + 1 <= m && !obstacleGrid[i - 1][j - 1]) dp[i][j] += dp[i + 1][j];
            }
        }
        return obstacleGrid[m - 1][n - 1] == 1 ? 0 : dp[1][1];
    }
};
```
***
##70.  Climbing Stairs(水dp)
非常水的动态规划,定义$dp[i]$为爬到$i$梯需要的步数,归纳为它的两个子问题即可.
```
class Solution {
public:
    int climbStairs(int n) {
        int dp[1000000];
        dp[0] = dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
};
```
***
##98. Validate Binary Search Tree(dfs)
**题意**:判断一个二叉树是不是二叉搜索树.
**分析**:中序遍历二叉搜索树,将得到升序数组,dfs遍历即可.

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    bool ans;
    long long last;
public:
    void dfs(TreeNode *cur) {
        if (!ans) return;
        if (cur -> left != NULL) dfs(cur -> left);
        if (cur -> val <= last) ans = false;
        last = cur -> val;
        if (cur -> right != NULL) dfs(cur -> right);
        return;
    }

    bool isValidBST(TreeNode* root) {
        ans = true, last = -10000000000000;
        if (root == NULL) return ans;
        dfs(root);
        return ans;
    }
};

```
##120. Triangle(dp)
**题意**:经典DP问题,有点像个二叉树的树形dp.
```
class Solution {
public:
    int minimumTotal(vector<vector<int>>& v) {
        int dp[222][222];
        
        for (int i = v[v.size() - 1].size() - 1; i >= 0; i--) {
            dp[v.size() - 1][i] = v[v.size() - 1][i];
        }
        for (int i = v.size() - 2; i >= 0; i--) {
            for (int j = v[i].size() - 1; j >= 0; j--) {
                dp[i][j] = v[i][j] + min(dp[i + 1][j], dp[i + 1][j + 1]);
            }
        }
        return dp[0][0];
    }
};
```
***
##208. Implement Trie (Prefix Tree)(trie树)
**题意**:支持插入查询,和前缀查询的trie树实现.
**分析**:设置一个前缀标记就好了.
这里应该记住一个经典操作,trie树中的迭代.
> ```
>  if ((p -> next)[word[i] - 'a'] == NULL) (p -> next)[word[i] - 'a'] = new TrieNode;
> p = (p -> next)[word[i] - 'a'];
> ```

**code**:
```

class TrieNode {
public:
    // Initialize your data structure here.
    TrieNode() : isword(false), ispreix(false) {
        for (int i = 0; i < 26; i++) next[i] = NULL;
    }
    bool isword, ispreix;
    TrieNode *next[26];
};

class Trie {
public:
    Trie() {
        root = new TrieNode();
    }

    // Inserts a word into the trie.
    void insert(string word) {
        TrieNode *p = root;
        for (int i = 0; i < word.size(); i++) {
            if ((p -> next)[word[i] - 'a'] == NULL) (p -> next)[word[i] - 'a'] = new TrieNode;
            p = (p -> next)[word[i] - 'a'];
            (p -> ispreix) = true;
        }
        (p -> isword) = true;
    }

    // Returns if the word is in the trie.
    bool search(string word) {
        TrieNode *p = root;
        for (int i = 0; i < word.size(); i++) {
            if ((p -> next)[word[i] - 'a'] == NULL) return false;
            p = (p -> next)[word[i] - 'a'];
            if (i == word.size() - 1) return (p -> isword);
        }
    }

    // Returns if there is any word in the trie
    // that starts with the given prefix.
    bool startsWith(string word) {
        TrieNode *p = root;
        for (int i = 0; i < word.size(); i++) {
            if ((p -> next)[word[i] - 'a'] == NULL) return false;
            p = (p -> next)[word[i] - 'a'];
            if (i == word.size() - 1) return (p -> ispreix);
        }
    }

private:
    TrieNode* root;
};


```
***
##211.Add and Search Word - Data structure design(trie树)
**题意**:创建一个单词库,支持两种操作,给库里面加入新单词和查询某单词是否在库中,但是注意用'.'来做通配符,可以代替任意字符.
**分析**:trie树的改造.主要是通配符的处理.这里把搜索函数处理了一下,用递归实现,当配件通配符号的时候遍历它的所有26个分枝来求满足条件的.

**code**:
```cpp

class WordDictionary {
private:
    struct node {
        int have;
        node *next[26];
        
        node(void) {
            have = 0;
            for (int i = 0; i < 26; i++) next[i] = NULL;
        }
    };
    
public:
    
    node *root = new node;

    // Adds a word into the data structure.
    // The time consumer is O(n)
    void addWord(string word) {
        node *p = root;
        for (int i = 0; i < word.size(); i++) {
            int aim = word[i] - 'a';
            if ((p -> next)[aim] == NULL) (p -> next)[aim] = new node;
            p = (p -> next)[aim];
        }
        (p -> have) = (p -> have) + 1;
    }
    
    bool can(node *p, int head, const string &word) {
        if (head == word.size() && p -> have) return true;
        int aim = word[head] == '.' ? 26 : word[head] - 'a';
        if (aim == 26) {
            for (int i = 0; i < 26; i++)
                if ((p -> next)[i] != NULL && can( (p -> next)[i], head + 1, word)) return true;
            return false;
        }
        if ((p -> next)[aim] != NULL)
            return can((p -> next)[aim], head + 1, word);
        return false;
    }
    
    // Returns if the word is in the data structure. A word could
    // contain the dot character '.' to represent any one letter.
    bool search(string word) {
        node *p = root;
        int head = 0;
        return can(p, head, word);
    }
};

// Your WordDictionary object will be instantiated and called as such:
// WordDictionary wordDictionary;
// wordDictionary.addWord("word");
// wordDictionary.search("pattern");



```
***
##264. Ugly Number II(dp,O(n)求丑数)
**题意**:求第n个丑数.
**分析**:很容易有set的做法,复杂度$nlogn$.但是这里可以用$O(n)$的做法.
- 我们定义$dp[i]$为第$i$个丑数,那么$dp[i]$一定是前面的某个丑数乘以2或者乘以3或者乘以5得到的最小值.
- 我们定义指针:p2,$dp[p2]$表示还没有乘以2来派生下一个数的最小丑数,同理有$dp[p3],dp[p5]$.这样我们就维护了伤着派生情况的最小值,每次都只需要从三个派生口找一个最小的来更新.
- 派送口的更新规则是一旦该口派生了,指针就指向下一个丑数,成为新的没有派生的最小丑数.

**总结**:这个思路还是极其巧妙的,有三条派生流,每次更新流的端口.
```
typedef long long ll;

class Solution {
public:

    ll nthUglyNumber(int n) {
        //priority_queue<int, vector<int>, greater<int> > que;
        set<ll> que;
        que.insert(1);
        for (int i = 1; i < n; i++) {
            ll top = *que.begin(); que.erase(top);
            if (!que.count(top * 2)) que.insert(top * 2);
            if (!que.count(top * 3)) que.insert(top * 3);
            if (!que.count(top * 5)) que.insert(top * 5);
        }
        return *que.begin();
    }
};
```

```
typedef long long ll;

class Solution {
public:

    ll nthUglyNumber(int n) {
        //priority_queue<int, vector<int>, greater<int> > que;
        ll ans[n];
        ans[0] = 1;
        int p2 = 0, p3 = 0, p5 = 0;
        for (int i = 1; i < n; i++) {
             ans[i] = min(ans[p2] * 2, min(ans[p3] * 3, ans[p5] * 5));
             if (ans[i] / ans[p2] == 2) p2++;
             if (ans[i] / ans[p3] == 3) p3++;
             if (ans[i] / ans[p5] == 5) p5++;
        }
        return ans[n - 1];
    }
};
```
***
##303. Range Sum Query - Immutable(水dp)
无它,水区间和...
```
class NumArray {
public:
    long long dp[100000];
    int size;
    NumArray(vector<int> &nums) {
        if (!nums.size()) return;
        dp[0] = nums[0];
        size = nums.size();
        for (int i = 1; i < nums.size(); i++) {
            dp[i] = 0;
            dp[i] = dp[i - 1] + nums[i];
        }
    }

    long long sumRange(int i, int j) {
        if (!size) return 0;
        if (!i) return dp[j];
        else return dp[j] - dp[i - 1];
    }
};


// Your NumArray object will be instantiated and called as such:
// NumArray numArray(nums);
// numArray.sumRange(0, 1);
// numArray.sumRange(1, 2);
```
