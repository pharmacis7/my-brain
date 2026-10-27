# Kth largest element in an Array

Example 1:

Input: nums = [3,2,1,5,6,4], k = 2
Output: 5


- take min heap 
- iterate in array and fill values in min heap in this filling if minheap size > k pop the top value (smallest) from min heap

```java
public int findKthLargest(int[] nums, int k) {
	PriorityQueue<Integer> minHeap = new PriorityQueue<>();
	Integer n = nums.length;
	for(int i = 0; i < n; i++){
		minHeap.offer(nums[i]);
		if(minHeap.size() > k){
			minHeap.poll(); //in java we can just ignore the return value wont throw exception
		}
	}
	return minHeap.peek();

	// Get the smallest element without removing it
	// System.out.println(minHeap.peek());  // 5

	// Remove and return the smallest element
	// System.out.println(minHeap.poll());  // 5
}
```


### time complexity 

iterate in array -> $O(n)$
inserting in min heap -> $O(log(m))$ where $m$ is size of minheap after insertion 
find size of heap -> $O(1)$
the max heap size can be $k$
removing element from heap -> $O(log(k))$ i only remove when heap size is k
total removal cost -> $O((n-k)log(k))$ 
total insertion cost -> $O(klog(k))$ 
total cost -> $O(n*log(k))$


space -> max heap size $O(k)$