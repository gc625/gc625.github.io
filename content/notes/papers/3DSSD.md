---
title: "3DSSD: Point-based 3D Single Stage Object Detector"
---

[arXiv link](https://arxiv.org/pdf/2002.10187.pdf) 
github #todo 


## Main Ideas:
-  Introduced **Feature-Farthest Point Sampling (F-FPS)**
- Introduced new sampling method called **Fusion Sampling** in [notes/papers/PointNet++#Set Abstraction SA Layer](Set Abstraction Layer)


### Feature-Farthest Point Sampling (F-FPS)
- Objective when downsample: 
	1. Remove **negative points** (background points) 
	2. Preserve only **positive points** (foreground points, i.e: points within any instance := ground truth box)
- Therefore leverage semantic features of points as well when applying FPS 
- Given two points $A$ and $B$, the criterion used to compare them in FPS is:$$C(A,B)=\lambda L_d(A,B)+L_f(A,B)$$
		where 
		- $L_d(A,B)$ is $L^2$ euclidean distance (xyz) 
		- $L_f(A,B)$ is $L^2$ feature distance (distance between the two feature vectors)
		- $\lambda$ is chosen parameter, paper seems to choose $\lambda=1$
		- *reminder*: $L^2(A,B)= \sqrt{(B_1-A_1)^2+(B_1-A_1)^2+\cdots+(B_n-A_n)^2}$  if $A$ and $B$ are $n$ dimensional vectors
- Result should be a subset of points that are less redundant and more diverse, as points are not only physically distant when sampling, but also in feature space.  

### Fusion Sampling
- Downsampling to $N_m$ points with **F-FPS** results in:
		- lots of positive points -> good for regression 
		- few negative points (due to limiting ) -> bad for classification
		- **Why?** Negative points don't have enough neighbours #expand 
- Input: $N_i\times C_i :=$ $N$ points each with feature vector of length $C$
- want to output $N_{i+1}$ points, where $N_{i+1}$ points are subset of the $N_i$ points
	1. F-FPS$: N_i \to \frac{N_{i+1}}{2}$ #Q
	2. D-FPS:$N_i \to \frac{N_{i+1}}{2}$ #Q
	3. [notes/papers/PointNet++#Grouping Layer](grouping operation )
	4. MLP
	5. MaxPool

![[notes/images/3dssd_backbone.png]]
#### SA1

$[16384 \times 4] \to [4096 \times 128]$

####  SA2 
$[4096 \times 128]\to [512\times 256],[512\times 256]$



