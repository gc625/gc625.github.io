---
title: "PointNet++"
---
# PointNet++



## Set Abstraction (SA) Layer

![[notes/research/images/pointnet2.png]]
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