# inorder traversal 

```java
ArrayList<Integer> temp = new ArrayList<>();
Stack<TreeNode> stack = new Stack<>();
TreeNode curr = root;

while(curr != null || !stack.isEmpty()){
	if(curr != null){
		stack.push(curr);
		curr = curr.left;
	}
	curr = stack.pop();
	temp.add(curr.val);
	curr = curr.right;
}
```

- time -: $O(n)$
- space -: max elements in stack left skewed tree $O(n)$

# LCA of binary search Tree 

At every node, compare p and q with the current root.
1. Both are smaller than root: Both nodes must be on the left, so move to root.left.
2. Both are bigger than root: Both nodes must be on the right, so move to root.right.
3. One is smaller and one is bigger, or one equals root: You have found the LCA. Return the current root.

```java
if(curr.val == p || curr.val == q){
	return curr;
}
if(curr.val > p && curr.val < q){
	return curr;
}
else if(curr.val > p && curr.val > q){
	curr = curr.left;
}
else{
	curr = curr.right;
}
```

# smallest missing genetic value 



