## 80. Median
####来源：[LintCode 80. Median](http://www.lintcode.com/en/problem/median/)

####Description：
Given a unsorted array with integers, find the median of it.

A median is the middle number of the array after it is sorted.

If there are even numbers in the array, return the N/2-th number after sorted.
####Example
Given `[4, 5, 1, 2, 3]`, return `3`.

Given `[7, 9, 4, 5]`, return `5`.

####Input and output analysis:
#####题目大意：
求给定数组的`nums[]`的中位数。
####Approach#1 库函数排序
#####算法思路：
对数组进行排序，拍完序后该数组中位数为`nums[(nums.length -1) / 2]`
#####实现代码
<pre><code>
public class Solution {
    /*
     * @param : A list of integers
     * @return: An integer denotes the middle number of the array
     */
    public int median(int[] nums) {
        // write your code here
        Arrays.sort(nums);
        return nums[(nums.length - 1) / 2];
    }
}</code></pre>
#####复杂度：

Time | Space | 
------------ | ------------ | 
O( $$$nlogn$$$ )| O( $$$?$$$ )|
####Approach#2 quickSelection
#####算法思路：
使用`quickSelection`算法选择第`(nums.length -1) / 2`个数。详见[LintCode 5. Kth Largest Element](http://www.lintcode.com/en/problem/kth-largest-element/)
#####实现代码
<pre><code>
public class Solution {
    public static void exch(int[] nums, int i, int j){
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }

    public static int lomutoPartition(int[] nums, int lo, int hi){
        int pivotValue = nums[lo];
        int i = lo, j = lo + 1;
        while (j <= hi){
            if (nums[j] < pivotValue)
                exch(nums,++i,j);
            ++j;
        }
        exch(nums,i,lo);
        return i;
    }
    
    public int median(int[] nums) {
        // write your code here
        int m = (nums.length-1) / 2;
        int lo = 0, hi = nums.length - 1;
        int pivot = lomutoPartition(nums,lo,hi);
        while (pivot != m){
            if (pivot > m){
                hi = pivot - 1;
            }else{
                lo = pivot + 1;
            }
            pivot = lomutoPartition(nums,lo,hi);
        }
        return nums[m];
    }
}</code></pre>
#####复杂度：

Time | Space | 
------------ | ------------ | 
O( $$$n$$$ )| O( $$$1$$$ )|
