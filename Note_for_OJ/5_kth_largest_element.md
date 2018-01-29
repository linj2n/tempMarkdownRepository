## 5. Kth Largest Element
####来源：[LintCode 5. Kth Largest Element](http://www.lintcode.com/en/problem/kth-largest-element/)
####Description：
Find K-th largest element in an array.
####Example
In array `[9,3,2,4,8]`, the 3rd largest element is `4`.

In array `[1,2,3,4,5]`, the 1st largest element is `5`, 2nd largest element is `4`, 3rd largest element is `3` and etc.
####Input and output analysis:
#####题目大意：
给定一个`int[]`与`k`输出第`k`个最大的数。
####Approach#1 对数组进行排序得出第`k`个最大的数
#####算法思路：
显然，若对`int[]`进行排序便可以很方便的根据下标找出第`k`个最大的数。当数组从小到大排好序后，第`k`个最大的数，就是`nums[nums.length - k]`。
#####实现代码
<pre><code>
class Solution {
    public int kthLargestElement(int k, int[] nums) {
        // write your code here
        Arrays.sort(nums);
        return nums[nums.length - k];
    }
};
</code></pre>
#####复杂度：

Time | Space | 
------------ | ------------ | 
O( $$$nlogn$$$ )| O( $$$1$$$ )|
####Approach#2 quickSelection
#####算法思路：
我们只需要找出第`k`个最大的数，若对整个数组进行排序未免有点浪费时间。这里可以采用用划分的思路，假设数组已经是有序的，求出第`k`个最大的数在排好序的数组中的最终位置，即`kIndex = nums.length - k;`。在数组随机选取一个`p`，根据`p`的值对数组进行整理，使得`p`的左边部分的元素全部`< p`，右边部分的元素`>= p`。然后比较`kIndex`与`p`，若`p`大了，则在`p`左边部分继续划分，`p`小了，则在右边部分划分。更新`p`值，继续比较，知道`kIndex == p`，此时`nums[kIndex]`就是第`k`个最大数。

#####实现代码
<pre><code>
import java.util.*;
class Solution {
    public static void exch(int[] nums, int i, int j)
    {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
    public static int lomutoPartition(int[] nums, int lo, int hi){
        Random r = new Random();
        int p = lo + r.nextInt(hi - lo + 1);
        int pivotValue = nums[p];
        exch(nums,lo,p);
        int i = lo, j = lo + 1;
        while (j <= hi){
            if (nums[j] < pivotValue)
                exch(nums,++i,j);
            ++j;
        }
        exch(nums,i,lo);
        return i;
    }
    public int kthLargestElement(int k, int[] nums) {
        // write your code here
        int seqKIndex = nums.length - k, lo = 0, hi = nums.length-1;
        int pivot = lomutoPartition(nums,lo,hi);
        while (pivot != seqKIndex){
            if (pivot > seqKIndex){
                hi = pivot - 1;
            }else{
                lo = pivot + 1;
            }
            pivot = lomutoPartition(nums,lo,hi);        
        }
        return nums[seqKIndex];
    }
};
</code></pre>
#####复杂度：

Time | Space | 
------------ | ------------ | 
O( $$$n$$$ )| O( $$$1$$$ )|
