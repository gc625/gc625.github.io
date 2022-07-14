---
title: "3DSSD: Point-based 3D Single Stage Object Detector"
---

[arXiv link](https://arxiv.org/pdf/2002.10187.pdf) 
github #todo 


## Main Ideas:
-  Introduced **Feature-Farthest Point Sampling (F-FPS)**
- Introduced new sampling method called **Fusion Sampling** in [Set Abstraction Layer](notes/papers/PointNet++)


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
	3. [grouping operation](notes/papers/PointNet++)
	4. MLP
	5. MaxPool

![[notes/images/3dssdbackbone.png]]




# Network walkthrough
using the following network config: ![[notes/images/3dssdcfg.png]]


## Network Input:
- Radar pts of dim 4: $[x,y,z,RCS]$
- **input size** is determined by `sample_points` in `DATA_PROCESSOR`: e.g.: 512 
- then the feature dimension is 1 (RCS)
### Assumptions
- For each layer, the points remaining $>$ `npoints`. If not, there'll be some padding/repeated points during downsampling.  
### syntax
$B$: is batch size
## 3DSSD Backbone 
### SA_Layer 1 (D-FPS)
**Input:**
- xyz: (B,512,3) <- `npoints=512` 
- feature:  (B,1,512) <- 1 feature for 512 pts

**Operations**
1. D-FPS to sample 512 points
   
3. Grouping: create `new_feature_list`
	1. ball query with r=0.2, nsample=<span style="color: RoyalBlue">32</span>, MLP=\[16,16,<span style="color: red">32</span>\] 
		- new_feauture: (B,<span style="color: red">32</span>,<span style="color: green">512</span>,<span style="color: RoyalBlue">32</span>) 
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

**Output:**
- new_xyz: (B,512,3)
- new_feature: (B,64,512) <- 64 features for 512 pts, etc... 

### SA_Layer 2 (FS)
**Input**
- xyz: (B,512,3) <-`npoint=512`
- feature: (B,64,512) <- 64 features from layer 1 
  
**Operations**
1. Sample 512 points via D-FPS and F-FPS, then concat them together (total pts=<span style="color: green">1024</span>) 
	- Note: unlike sampling via \[F-FPS,D-FPS\] (see next layer), it seems like FS may select the same point more than once. 
2. Grouping
	1. ball query with r=0.4, nsample=<span style="color: RoyalBlue">32</span>, then  MLP=\[64,64,<span style="color: red">128</span>\] 
		- new_feauture: (B,<span style="color: red">128</span><span style="color: green">1024</span>,<span style="color: RoyalBlue">32</span>)  <- the +3 here is xyz of each sampled point
	2. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">128</span>,<span style="color: green">1024</span>), append to `new_feature_list`
		  
	3. ball query with r=0.8, nsample=<span style="color: RoyalBlue">32</span>, then MLP=\[64,64,<span style="color: red">128</span>\] 
		- new_feauture: (B,<span style="color: red">128</span>,<span style="color: green">1024</span>,<span style="color: RoyalBlue">32</span>)  <- the +3 here is xyz of each sampled point
	4. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">128</span>,<span style="color: green">1024</span>), append to `new_feature_list`
		  
	5. ball query with r=1.6, nsample=<span style="color: RoyalBlue">64</span>, then MLP=\[64,96,<span style="color: red">128</span>\] 
		- new_feauture: (B,<span style="color: red">128</span>,<span style="color: green">1024</span>,<span style="color: RoyalBlue">64</span>)  <- the +3 here is xyz of each sampled point
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

### SA_Layer 3 (F-FPS, D-FPS) 
- Not sure why we use F-FPS and D-FPS instead of just FS, I think this is to make sure the set of points sampled $F\text{-}FPS \cap D\text{-}FPS =\emptyset$ ![[notes/images/samplingrange.png]]
	- so in this layer, F-FPS samples `[0:512]` pts, then D-FPS samples from `[512:1024]`
	- but then wouldn't this cause some sampling bias? if there are good foreground pts in `[512:1024]` then F-FPS cant sample them

**Input**
- xyz: (B,1024,3)
- feature: (B,128,1024)

**Operations**
1. Sample 256 points via D-FPS and F-FPS, then concat them together (total pts=<span style="color: green">512</span>) 

2. Grouping 
	1. ball query with r=1.6, nsample=<span style="color: RoyalBlue">32</span>, then  MLP=\[128,128,<span style="color: red">256</span>\] 
		- new_feauture: (B,<span style="color: red">256</span>,<span style="color: green">512</span>,<span style="color: RoyalBlue">32</span>)  <- the +3 here is xyz of each sampled point
	2. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">256</span>,<span style="color: green">512</span>), append to `new_feature_list`

	3. ball query with r=3.2, nsample=<span style="color: RoyalBlue">32</span>, then  MLP=\[128,196,<span style="color: red">256</span>\] 
		- new_feauture: (B,<span style="color: red">256</span>,<span style="color: green">512</span>,<span style="color: RoyalBlue">32</span>)  <- the +3 here is xyz of each sampled point
	4. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">256</span>,<span style="color: green">512</span>), append to `new_feature_list`
		  
	5. ball query with r=4.8, nsample=<span style="color: RoyalBlue">32</span>, then  MLP=\[128,256,<span style="color: red">256</span>\] 
		- new_feauture: (B,<span style="color: red">256</span>,<span style="color: green">512</span>,<span style="color: RoyalBlue">32</span>)  <- the +3 here is xyz of each sampled point
	6. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">256</span>,<span style="color: green">512</span>), append to `new_feature_list`
		  
3. Aggregation Channel:
	1. `torch.cat` all features along `dim=1`
		-  new_feature: (B,<span style="color:red">256+256+256</span>,<span style="color: green">512</span>)
	2. Conv1d with `in_channel=768`, `out_channel=256`, `kernel_size = 1`, batchnorm1d and ReLU 
		- new_feature: (B,<span style="color: red">256</span>,<span style="color: green">512</span>)
		  
**Output**
- new_xyz: (B,512,3)
- new_feature: (B,256,512)

### SA_Layer 4 (F-FPS, D-FPS)
 **THIS IS THE FIRST PART OF CANDIDATE GENERATION**
**Input**
- xyz: (B,512,3)
- feature: (B,256,512)

**Operations**
  
**Output:**
- new_xyz: (B,128,3)
- new_feature: (B,256,128)

### Vote_Layer (n/a)
  **THIS IS THE SECOND PART OF CANDIDATE GENERATION** 
 
**Input:**
- xyz: (B,128,3)
- feature: (B,256,128)
  
**Output:**
- new_xyz: (B,128,3)
- new_feature: (B,128,128)
- ctr_offset: (B,128,3)

### SA_Layer5 (D-FPS)
**Input**
- xyz: (B,512,3) <- output of SA_Layer 3
- feature: (B,256,512) <- output of SA_Layer 3
- ctr_xyz: (B,127.3) <- output from vote layer 

**Operationa**
1. Sample 128 points via D-FPS (total pts=<span style="color: green">128</span>) 

2. Grouping 
	1. ball query with r=4.8, nsample=<span style="color: RoyalBlue">16</span>, then  MLP=\[256,256,<span style="color: red">512</span>\] 
		- new_feauture: (B,<span style="color: red">512</span>,<span style="color: green">128</span>,<span style="color: RoyalBlue">16</span>)  <- the +3 here is xyz of each sampled point
	2. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">512</span>,<span style="color: green">128</span>), append to `new_feature_list
	3. ball query with r=4.8, nsample=<span style="color: RoyalBlue">32</span>, then  MLP=\[256,512,<span style="color: red">1024</span>\] 
		- new_feauture: (B,<span style="color: red">1024</span>,<span style="color: green">128</span>,<span style="color: RoyalBlue">32</span>)  <- the +3 here is xyz of each sampled point
	4. Maxpool and squeeze last channel `[-1]`
		- new_feature: (B,<span style="color: red">1024</span>,<span style="color: green">128</span>), append to `new_feature_list

3. Aggregation Channel:
	1. `torch.cat` all features along `dim=1`
		-  new_feature: (B,<span style="color:red">512+1024</span>,<span style="color: green">128</span>)
	2. Conv1d with `in_channel=1536`, `out_channel=512`, `kernel_size = 1`, batchnorm1d and ReLU 
		- new_feature: (B,<span style="color: red">512</span>,<span style="color: green">128</span>)

**Output**
- new_xyz: (B,128,3)
- new_feature: (1,512,128)

# Detection head
- For box prediction and classification, we take the center features from the last layer of the backbone (B,<span style="color: red">512</span>,<span style="color: green">128</span>) reshaped into: (B*<span style="color: green">128</span>,<span style="color: red">512</span>) and feed it into two MLPs.

## Box prediction 
- MLP = \[<span style="color: RoyalBlue">256</span>,<span style="color: LightBlue">256</span>\]
1. FC: in_channel=<span style="color: red">512</span>, out_channel=<span style="color: RoyalBlue">256</span>
2. BN then ReLU
3. FC: in_channel=<span style="color: red">256</span>, out_channel=<span style="color: LightBlue">256</span>
4. BN then ReLU 
5. FC: in_channel=<span style="color: LightBlue">256</span>, out_channel=30 

**Output:** 
- (B*<span style="color: green">128</span>,30)

**note**
- the 30 is:
	- (x,y,z,dx,dy,dz), + 2* (12 angle bins ) 
		- angle bins * 2 because positive and negative angles? 

## Box classification 
- MLP = \[<span style="color: RoyalBlue">256</span>,<span style="color: LightBlue">256</span>\]
1. FC: in_channel=<span style="color: red">512</span>, out_channel=<span style="color: RoyalBlue">256</span>
2. BN then ReLU
3. FC: in_channel=<span style="color: red">256</span>, out_channel=<span style="color: LightBlue">256</span>
4. BN then ReLU 
5. FC: in_channel=<span style="color: LightBlue">256</span>, out_channel=3 <- number of classes 

**Output**
- (B*<span style="color: green">128</span>,3)


## Target assignment
- After the two sets of MLP above, we need to assign predicted boxes to gt boxes. this is done via `assign_targets(self, input_dict):`

1. Take GT boxes and enlarge them by `GT_EXTRA_WIDTH: [0.2, 0.2, 0.2]`
2. call   `assign_stack_targets()`  with params:
	- `points = centers`, centers are predicted centers with shape (B\*128,4)
	- `gt_boxes = gt_boxes`,   GT boxes with shape (B,G,8) <- G = number of boxes per scene, 8: xy,z,dx,dy,dz,angle,class
	- `extend_gt_boxes=extend_gt_boxes`: enlarged GT boxes with shape (B,G,8)
	- `set_ignore_flag = True`: not sure what this is used atm...
	- `use_ball_constraint = False`: not sure 
	- `ret_part_labels = False`:  not sure
	- `ret_box_labels = True `:  but not sure why
	1. For each scene in the batch, run `roiaware_pool3d_utils.points_in_boxes_gpu()` to find the predicted centroids that lie inside a gt box. 
		- return shape is (128): values are either idx of the gt box it lies in, or -1 if its background 
		- create flag: `box_fg_flag = (box_idxs_of_pts >= 0)`
	2. if `set_ignore_flag = True`, then do the same for the extended gt boxes. 
		- `fg_flag = box_fg_flag
		- `ignore_flag = fg_flag ^ (extend_box_idxs_of_pts >= 0)`
			- note that `^` is [xor gate](https://www.maximintegrated.com/content/dam/images/glossary/xor-gate-symbol.jpg) in other flag, we only want to ignore flag points that are **ONLY** in the extended gt box, or **ONLY** in the gt box 
		- then `point_cls_labels_single[ignore_flag] = -1`, 
		- `gt_box_of_fg_points = gt_boxes[k][box_idxs_of_pts[fg_flag]]` 
			- `box_idxs_of_pts[fg_flag]`: is a 1D tensor with indices of the gt box each pt lies in. e.g: \[0,4,2,4,0,1,...\]
			- then `gt_box_of_fg_points` is a 2D tensor of shape (M,8) where M is the number of pts that are inside a gt box, with the associated gt box. 
		- `point_cls_labels_single[fg_flag] = 1 if self.num_class == 1 else gt_box_of_fg_points[:, -1].long()`
			- \[-1\] is the class of the gt, so this gets the label and puts it into a 1D tensor.
	3. if `ret_box_labels and gt_box_of_fg_points.shape[0] > 0:`, i.e. if there are points that lie inside gt boxes,
		- call `fg_point_box_labels =  self.box_coder.encode_torch()` with params:
			-  `gt_boxes=gt_box_of_fg_points[:, :-1]`: so just all boxes, with x,y,z,dx,dy,dz,angle
			- `points=points_single[fg_flag]`, all predicted centers that lie inside gt box
			- `gt_classes=gt_box_of_fg_points[:, -1].long()` class of gt boxes. 
				- but not used if `self.use_mean = False` , this is set via `'use_mean_size': False` under  `BOX_CODER_CONFIG`
		- function basically assigned a label based on residuals for each pt based on the gt box it lies in.
			- i.e. the label for each point is different between point (x,y,z) and box center +
			- log() of dx dy dz of gt box
			- bin of angle + residual
			- total = length of 8 
	4. `point_box_labels_single[fg_flag] = fg_point_box_labels` 
		-  assign the points their new labels 
	5. `point_box_labels[bs_mask] = point_box_labels_single`
		- assign it to the "outer" list (where all labels for each sample in the batch will be) 
	6. after doing this for all samples in batch:  concat all `gt_box_of_fg_points` into a tensor `gt_boxes_of_fg_points` 
	7.  Return the following dict. 
```
targets_dict = {
'point_cls_labels': point_cls_labels, # (B*128)
'point_box_labels': point_box_labels, # (B*128,8)
'point_part_labels': point_part_labels, # None
'box_idxs_of_pts': box_idxs_of_pts, # (128)
'gt_box_of_fg_points': gt_boxes_of_fg_points,#(M,8), M is the number of pts that 
					     # are inside a gt box, so this is list
						 # since it changes for every batch
}
```

 

		
			 