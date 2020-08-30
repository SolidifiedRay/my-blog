---
title: Study Notes - Binary Search
date: 2020-08-29
categories:
- Study Notes
tags:
- Algorithm
---

Binary search is a very efficient algorithm for finding an element in a sorted list.  

First we compare the middle element with the target. If the target is smaller than the middle element, we reduce our search space to the first half of the list. If the target is bigger than the middle element, we reduce our search space to the second half of the list. We will keep reducing our search space until we find the target or the search space reach its minimum size.

There are two important rules for Binary Search:
1. we must reduce our search space after each iteration.  
2. we must make sure that the target cannot be ruled out when we reduce the search space.   

Here is the code for a classical Binary Search:  
{% highlight java %}
public int binarySearch(int[] array, int target){
	if (array == null || array.length == 0){
		return -1;
	}
	int left = 0;
	int right = 0;
	while (left <= right){
		int middle = left + (right - left) // 2;
		if (target < array[middle]){
			right = middle - 1;
		}
		else if (target > array[middle]){
			left = middle + 1;
		}
		else{
			// target == array[middle]
			return middle;
		}
	}
	return -1;
}
{% endhighlight %}

Time complexity is *O(logn)* because we reduced the search space by half during each iteration.  
Space complexity is *O(1)* because there is no extra space.  
<br/>
<br/>
We can also perform binary search in a sorted 2D matrix.  
A simple way to do this is to treat it as an array with mathematical operation.
<pre><code>left = 0  
right = the size of matrix - 1  
middle = left + (right - left)/2
</code></pre>
When we looping through the matrix:
<pre><code>current row = middle / column size  
current col = middle % colum size
</code></pre>
Here is the code:  
{% highlight java %}
public int[] binarySearchIn2DMatrix(int[][] matrix, int target){
	if (matrix == null || matrix[0].length == 0){
		return new int[] {-1, -1};
	}
	int row = matrix.length;
	int col = matrix[0].length;
	int left = 0;
	int right = row * col - 1;
	while (left <= right){
		int middle = left + (right - left)/2;
		int r = middle / col;
		int c = middle % col;
		if (target < matrix[r][c]){
			right = middle - 1;
		}
		else if (target > matrix[r][c]){
			left = middle + 1;
		}
		else{
			return new int[] {r, c};
		}
	}
	return new int[] {-1, -1};
}
{% endhighlight %}

The time complexity is O(logm×n) with a m×n matrix.  
<br/>
<br/>
What if there are duplicate elements and we need to find the index of the first occurrence of the target?  
Remember we must not rule out the target when we reduce our search space.  
When the value of the middle element is the same as the target, we should set the right index = middle instead of middle - 1, because the middle element may be the first occurrence of the target. If we set right = middle - 1, we may rule out the target.  
Furthermore, our left index should always less than the right index - 1. For example, If we have two elements [1, 2] and the target value is 2, we will never exit the loop if we use left <= right - 1 as the condition.  
Here is the code:  
{% highlight java %}
public int firstOccur(int[]  array, int target){
	if (array == null || array.length == 0){
		return -1;
	}
	int left = 0;
	int right = array.length - 1;
	while (left < right - 1){
		int middle = left + (right - left)/2;
		if (array[middle] < target){
			left = middle; // or left = middle + 1;
		}
		else{
			right = middle;
		}
	}
	if (array[left] == target){
		return left;
	}
	if (array[right] == target){
		return right;
	}
	return -1;
}
{% endhighlight %}
<br/>
<br/>
What if we want to find the index of the last occurence of the target? In this case, when we find array[middle] == target, we need to set left = middle.  
Here is the code:  
{% highlight java %}
public int lastOccur(int[]  array, int target){
	if (array == null || array.length == 0){
		return -1;
	}
	int left = 0;
	int right = array.length - 1;
	while (left < right - 1){
		int middle = left + (right - left)/2;
		if (array[middle] > target){
			right = middle; // or right = middle - 1;
		}
		else{
			left = middle;
		}
	}
	if (array[right] == target){
		return right;
	}
	if (array[left] == target){
		return left;
	}
	return -1;
}
{% endhighlight %}
<br/>
<br/>
Now, let's try to find the element that is closet to the target.
For example, given a list = [1, 5, 8] and target = 6. The result will be 1 since 5 is closet to 6.  
Again, we should not rule out the middle element because it can be the target we want. Also, we should reduce our search space to two elements and compare them with the target. So the condition for the loop will be left < right - 1.  
Here is the code:
{% highlight java %}
public int closest(int[] array, int target){
	if (array == null || array.length == 0){
		return -1;
	}
	int left = 0;
	int right = array.length - 1;
	while (left < right - 1){
		int middle = left + (right - left)/2;
		if (array[middle] < target){
			left = middle;
		}
		else if (array[middle] > target){
			right = middle;
		}
		else{
			return middle;
		}
	}
	if (Math.abs(array[left] - target) <= Math.abs(array[right] - target)){
		return left;
	}
	return right;
}
{% endhighlight %}
<br/>
<br/>
What if we want to find k closet elements to the target?  
We just need to find the index of the closet element. Then we find k closet elements from the middle to the side.  
Here is the code:
{% highlight java %}
public int kCloset(int[] array, int target){
	if (array == null || array.length == 0){
		return array;
	}
	int left = closest(array, target);
	int right = left + 1;
  	int[] result = new int[k];
	for (int i = 0; i < k; i ++){
		// We can move our left pointer when
		// 1. Right index is out of bound
		// 2. Left index is not out out of bound and array[left] is closer to the target
		if (right >= array.length || left >= 0 && target - array[left] <= array[right] - target){
			result[i] = array[left--];
		}
		else{
			result[i] = array[right++];
		}
	}
	return result;
}
{% endhighlight %}
The time complexity will be O(logn + k).  
<br/>
<br/>
Now, let's try to find the smallest element that is larget than the target.  
Again, we should not rule out the target. When array[middle] is larget than the target, it can still be the reuslt we want. Therefore right = mid instead of mid - 1.  
Here is the code:  
{% highlight java %}
public int smallestElementLargetThenTarget(int[] array, int target){
	if (array == null || array.length == 0){
		return -1;
	}
	int left = 0;
	int right = array.length - 1;
	while (left < right - 1){
		int middle = left + (right - left)/2;
		if (array[middle] <= target){
			left = middle + 1;
		}
		else{
			right = middle;
		}
	}
	if (array[left] > target){
		return left;
	}
	if (array[right] > target{
		return right;
	}
	return - 1;
}
{% endhighlight %}
<br/>
<br/>
Now given a dictionary of unkown size, and the number in this dictionary is sorted in ascending order. How can we use binary search to find the target?  
We need to find the right index of this dictionary. Here is a simple way to do this.  
{% highlight java %}
public int searchInUnknownSizedSortedArray(Dictionary dict, int target){
	if(dict == null){
		return -1;
	}
	int left = 0;
	int right = 1;
	while(dict.get(right) != null && dict.get(right) < target){
		left = right;
		right = 2 * right;
	}
	return binarySearch(dict, left, right, target);
}

public int binarySearch(Dictionary dict, int left, int right, int target){
	while (left <= right){
		int mid = left + (right - left)/2;
		if (dict.get(mid) == null || dict.get(mid) > target){
			right = mid - 1;
		}
		else if (dict.get(mid) < target){
			left = mid + 1;
		}
		else{
			return mid;
		}
	}
	return - 1;
}
{% endhighlight %}