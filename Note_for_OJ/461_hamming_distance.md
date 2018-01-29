## 461. Hamming Distance

####来源：[461. Hamming Distance](https://leetcode.com/problems/hamming-distance/description/)

####Description：
The Hamming distance between two integers is the number of positions at which the corresponding bits are different.

Given two integers x and y, calculate the Hamming distance.
####Example
Input: x = 1, y = 4

Output: 2

Explanation:<pre>1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
The above arrows point to positions where the corresponding bits are different.
</pre>


####Input and output analysis:
#####题目大意：

####Approach#1 按位操作
#####算法思路：
#####实现过程注意的问题：
#####实现代码
<pre><code>
class Solution {
    public int hammingDistance(int x, int y) {
        int xorRes = x ^ y;
        int count = 0;
        while (xorRes != 0) {
            xorRes = xorRes & (xorRes - 1);
            count ++;
        }
        return count;
    }
}
</code></pre>
#####复杂度：

Time | Space | 
------------ | ------------ | 
O( $$$-$$$ )| O( $$$-$$$ )|

