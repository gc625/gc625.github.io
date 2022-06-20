---
title: "PointNet++"
enableToc: true
---


## Set Abstraction (SA) Layer
![[notes/images/pointnet2.png]]

### Sampling Layer 
Lets call the sampling layer $SL$.
- Input: $N\times 3$ 
	- N points with $x,y,z$
- Output: $M\times 3$ 
	- $M$ points with $x,y,z$ where points in $M$ are subset of $N$
- Method: **Iterative Farthest Point Sampling** (FPS)
	1. Start by choosing 1 random point
	2. Calculate distance for all remaining points to selected points.
		1. so each point will have an array keeping track of the distances $$dist_i:[dist\_to\_selected_0,dist\_to\_selected_1,\ldots]$$
		   for example:$$dist_0=[1.1,3.5,25,23]$$
		   would be point $0$ and its distance to the four selected points.
		3. take the minimum of the arrray, i.e. for each remaining point, set its distance to the closest selected point. for exmaple, $$\begin{align}
		   dist_0 = 1.1
		\end{align}$$
		4. Finally, select the point with the greatest distance
	3. Repeat step 2 $M-1$ more times
	- **1D Example:** lets perform steps 1 and 2 on array of points $P=[1,6,7,8,20]$, 
		1. suppose the list of selected points $S=[7,20]$ , $P$ is now $[1,6,8]$
		2. we calculate distances:
		   $$\begin{align*}dist_1=[6,19]\\
		   dist_6=[1,14]\\
		   dist_8=[1,12]\\
		   \end{align*}$$
		3. perform $min (dist_i)$$$\begin{align*}dist_1=6\\
		   dist_6=1\\
		   dist_8=1\\
		   \end{align*}$$
		4. now select the point with highest distance. i.e. $$\underset{i}{\operatorname{argmax}}(dist_i)=1$$
		5. So the next chosen point is 1. $S=[1,7,20]$ and $P=[6,8]$



### Grouping Layer
- Sampling Layer $SL$ yields $N'\times 3$ points.
	- $N'$ centroids and $3:= x,y,z$ coordinates 
	- $SL:(N\times 3) \to (N'\times 3)$
- Input: 
	- $(N\times 3+ C)$: points before sampling and their feature vectors of len $C$
	-  $(N'\times d)$: predicted centroids 
- Output: 
	- $(N'\times K \times (3+C))$: centroid + $K$ points within radius of $r$ along their features
- Method: 
	- Ball query
			- what if points within radius is $<K$? 
			- what if no points (only centroid point)?
			- with if more than $K$ points?  



