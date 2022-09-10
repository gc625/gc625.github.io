---
title: "An End-to-End Transformer Model for 3D Object Detection"
---

2020, [link](https://openaccess.thecvf.com/content/ICCV2021/papers/Misra_An_End-to-End_Transformer_Model_for_3D_Object_Detection_ICCV_2021_paper.pdf)


# Intro
- Many detection models work directly on point clouds
	- turning unordered set of inputs (point cloud) into unordered set of outputs (bbx)
	- i.e VoteNet with encoder ([PointNet++](notes/papers/PointNet++)) and decoder architecture
		- effective but required years of careful development of hand-encoding inductive biases, radii, and designing special 3D operators and loss functions.
- recently, set-to-set encoder-decoder models also emerged in 2D object detection as a competitive method. (i.e. DETR)
- **Central question:** since transformers are permutation invariant (good for set-to-set problems), can we create a 3D object detector with it w/p hand-designed inductive biases?


# Related Work 

## Grid-based 3D Architectures
- convert irregular point clouds into 3D/2D grids (voxels) then apply convnets 


## Point Cloud Architectures 
- use pointnet++ operations (downsampling + MLPs + maxPool) on point clouds directly
- construct graphs (i.e: DGCNN, PointWeb)
- continous point convolutions (pointConv, KPConv)


# 3DETR: Encoder-decoder Transformer
- Input: $N$ points where each point is associated with $x,y,z$ coordinates 
- downsample input to points with $N'$ features via pointnet ops.
- pass $N'$ features into decoder to output bbx, 

## Encoder
- Input: points $(N,3+C)$
- SA: $(N,3+C)$ $\to$  $(N',d)$ where $d=256$ 
	- via $MLP(64,128,256)$  
- Attn: $(N',d)\to(N',d)$ multiple times


## Decoder
- Frame detection as a set prediction problem.
	- i.e: predict set of boxes w/o order 
- Parallel decoder takes in $N'$ point features and set of $B$ query embeddings $\{\mathbf{q}^e_1,\ldots,\mathbf{q}^e_B\}$ to produce $B$ feautres for bbx. 
- $\mathbf{q}^e$ represent locations in 3D space around which our final 3D bounding boxes are predicted. 
- positional embeddings is used 


## Non-parametric query embeddings 

