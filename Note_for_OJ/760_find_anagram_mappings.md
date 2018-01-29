## 760. Find Anagram Mappings
####来源：[760. Find Anagram Mappings](https://leetcode.com/problems/find-anagram-mappings/description/)

####Description：
Given two lists Aand B, and B is an anagram of A. B is an anagram of A means B is made by randomizing the order of the elements in A.

We want to find an index mapping P, from A to B. A mapping P[i] = j means the ith element in A appears in B at index j.

These lists A and B may contain duplicates. If there are multiple answers, output any of them.
####Example
For example, given

A = `[12, 28, 46, 32, 50]`
B = `[50, 12, 32, 46, 28]`
We should return
`[1, 4, 3, 2, 0]`
as `P[0] = 1 `because the 0th element of A appears at `B[1]`, and `P[1] = 4` because the 1st element of A appears at `B[4]`, and so on.

Note:

A, B have equal lengths in range [1, 100].22

`A[i]`, `B[i] `are integers in range [0, 10^5].
####Input and output analysis:
#####题目大意：

####Approach#1 Brute force
#####算法思路：
#####实现代码
<pre><code>
class Solution {
    public int[] anagramMappings(int[] A, int[] B) {
        int len = A.length;
        int[] ans = new int[len];
        for (int i = 0; i < len; i++) {
            for (int j = 0; j < len; j++) {
                if (A[i] == B[j]) {
                    ans[i] = j; break;
                }
            }
        }
        return ans;
    }
}
</code></pre>
#####复杂度：

Time | Space | 
------------ | ------------ | 
O( $$$n^2$$$ )| O( $$$1$$$ )|
####Approach#2 
#####算法思路：

#####实现代码
<pre><code>
import java.util.*;
class Solution {
        public int[] anagramMappings(int[] A, int[] B) {
            int[] ans = new int[A.length];
            Map <Integer, Integer> h = new HashMap<>();
            for (int i = 0; i < B.length; i++) {
                h.put(B[i],i);
            }
            for (int j = 0; j < A.length; j++) {
                ans[j] = h.get(A[j]);
            }
            return ans;      
        }
}
</code></pre>
#####复杂度：

Time | Space | 
------------ | ------------ | 
O( $$$-$$$ )| O( $$$-$$$ )|
