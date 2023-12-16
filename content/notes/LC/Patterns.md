---
title: "Patterns"
enableToc: true
---


# Binary Trees
## Definition
```Python
class node:
	def __init__(val,left=None,right=None)
		self.val = val
		self.left = left
		self.right = right
```

## DFS

```Python

def dfs(node):
	if node is None:
		return #base case

	# LOGIC 

	if root.left is not None:
		dfs(root.left)
	if root.right is not None:
		dfs(root.right)
```




## BFS

```Python
from collections import deque
# PSUEDOCODE

# 1. corner case
if node is empty:
	return # sth


queue =  deque([node])
# if need to keep track of depth or sth
queue =  deque([(node,depth or column)])


while len(queue) > 0:

	node = queue.popleft()

	if node is not None:
		# logic to node

		queue.append(node.left)
		queue.append(node.right)
	
return #answer

```



# 
