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

![[notes/images/3dssdbackbone.png]]

# Network walkthrough
using the following network config: ![[notes/images/3dssdcfg.png]]


## Network Input:
- Radar pts of dim 4: $[x,y,z,RCS]$
- **input size** is determined by `sample_points` in `DATA_PROCESSOR`: e.g.: 512 
- then the feature dimension is 1 (RCS)

## syntax
$B$: is batch size
## SA_Layer 1 (D-FPS)
**Input:**
- xyz: (B,512,3) <- `npoints=512` 
- feature:  (B,1,512) <- 1 feature for 512 pts
**Process**
1. D-FPS to sample 512 points
   
3. Grouping: create `new_feature_list`
	1. ball query with r=0.2, nsample=<span style="color: RoyalBlue">32</span>, MLP=\[16,16,<span style="color: red">32</span>\] 
		new_feauture: (B,<span style="color: red">32</span>,<span style="color: green">512</span>,<span style="color: RoyalBlue">32</span>) 
	2. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">32</span>,<span style="color: green">512</span>), append to `new_feature_list`
		
	3. ball query with r=0.4, nsample=<span style="color: RoyalBlue">32</span>, MLP=\[16,16,<span style="color: red">32</span>\] 
		- new_feauture: (B,<span style="color: red">32</span>,<span style="color: green">512</span>,<span style="color: RoyalBlue">32</span>) 
	4. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">32</span>,<span style="color: green">512</span>) , append to `new_feature_list`

	5. ball query with r=0.8, nsample=<span style="color: RoyalBlue">64</span>, MLP=\[16,16,<span style="color: red">32</span>\] 
		- new_feauture: (B,<span style="color: red">32</span>,<span style="color: green">512</span>,<span style="color: RoyalBlue">64</span>) 
	6. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">32</span>,<span style="color: green">512</span>) , append to `new_feature_list`

3. Aggregation Channel:
	1. `torch.cat` all features along `dim=1`
		-  new_feature: (B,<span style="color:red">32+32+64</span>,<span style="color: green">512</span>)
	2. Conv1d with `in_channel=128`, `out_channel=64`, `kernel_size = 1`, batchnorm1d and ReLU 
		- new_feature: (B,<span style="color: red">64</span>,<span style="color: green">512</span>)
		  ---
title: "{{title}}"
enableToc: true
---
**Output:**
- new_xyz: (B,512,3)
- new_feature: (B,64,512) <- 64 features for 512 pts, etc... 

## SA_Layer 2 (FS)
**Input**
- xyz: (B,512,3) <-`npoint=512`
- feature: (B,64,512) <- 64 features from layer 1 
**Process**
1. Sample 512 points via D-FPS and F-FPS, then concat them together (total pts=<span style="color: green">1024</span>)
2. Grouping
	1. ball query with r=0.4, nsample=<span style="color: RoyalBlue">32</span>, then  MLP=\[64,64,<span style="color: red">128</span>\] 
		- new_feauture: (B,<span style="color: red">128</span>+3,<span style="color: green">1024</span>,<span style="color: RoyalBlue">32</span>)  <- the +3 here is xyz of each sampled point
	2. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">128</span>,<span style="color: green">1024</span>), append to `new_feature_list`
		  
	3. ball query with r=0.8, nsample=<span style="color: RoyalBlue">32</span>, then MLP=\[64,64,<span style="color: red">128</span>\] 
		- new_feauture: (B,<span style="color: red">128</span>+3,<span style="color: green">1024</span>,<span style="color: RoyalBlue">32</span>)  <- the +3 here is xyz of each sampled point
	4. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">128</span>,<span style="color: green">1024</span>), append to `new_feature_list`
		  
	5. ball query with r=1.6, nsample=<span style="color: RoyalBlue">64</span>, then MLP=\[64,96,<span style="color: red">128</span>\] 
		- new_feauture: (B,<span style="color: red">128</span>+3,<span style="color: green">1024</span>,<span style="color: RoyalBlue">64</span>)  <- the +3 here is xyz of each sampled point
	6. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">128</span>,<span style="color: green">1024</span>), append to `new_feature_list`
 
3. Aggregation Channel:
	1. `torch.cat` all features along `dim=1`
		-  new_feature: (B,<span style="color:red">128+128+128</span>,<span style="color: green">1024</span>)
	2. Conv1d with `in_channel=384`, `out_channel=128`, `kernel_size = 1`, batchnorm1d and ReLU 
		- new_feature: (B,<span style="color: red">128</span>,<span style="color: green">1024</span>)

**Output**
- new_xyz: (B,1024,3)
- new_feature: (B,128,1024)

## SA_Layer 3 (F-FPS, D-FPS) 
LAYER
**Input**
- xyz: (B,1024,3)
- feature: (B,128,1024)
**Output**
- new_xyz: (B,512,3)
- new_feature: (B,256,5112)

## SA_Layer 4 (F-FPS, D-FPS)
### THIS IS THE FIRST PART OF CANDIDATE GENERATION
**Input:**
- xyz: (B,,)
- feature:
**Output:**
- new_xyz: 
- new_feature:

# Vote_Layer (n/a)
 ### THIS IS THE SECOND PART OF CANDIDATE GENERATION 
 
**Input:**
- xyz:
- feature:
**Output:**
- new_xyz: 
- new_feature:

