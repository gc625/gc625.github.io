---
title: "Not All Points Are Equal: Learning Highly Efficient Point-based Detectors for 3D LiDAR Point Clouds"
enabletoc: true
---

# Main Ideas 
- Turns out [F-FPS](notes/papers/3DSSD) from 3DSSD is still not good enough in preserving foreground points. 
	- Introduced Class-aware Sampling



# Class-Aware Sampling
- Superior sampling method compared to [F-FPS](notes/papers/3DSSD)  and [D-FPS](notes/papers/PointNet++)
-  Using vanilla cross-entropy loss $$L_{cls-aware}=-\sum^{C}_{c=1}(s_i\log(\hat{s_i})+(1-s_i)\log(1-\hat{s_i}))$$
- $C:$ Number of catergories 
- $s_i:$ One hot labels 
- $\hat{s_i}:$ predicted logits  


# Centroid Aware Sampling
- Give higher weight to points near instance centroid $$Mask_i=\sqrt[3]{\frac{\min(f^*,b^*)}{\max(f^*,b^*)}\times \frac{\min(l^*,r^*)}{\max(l^*,r^*)}\times\frac{\min(u^*,d^*)}{\max(u^*,d^*)}}$$
- $f^∗, b^∗, l^∗, r^∗, u^∗, d^∗$ represent the distance of a point to the 6 surfaces (front, back, left, right, up and down)
- This mask is used during **training** via the ctr-aware loss $L_{ctr-aware}$. At inference we simply keep top $k$ points with highest scores. $$L_{ctr-aware}=-\sum^{C}_{c=1}(Mask_i\cdot s_i\log(\hat{s_i})+(1-s_i)\log(1-\hat{s_i}))$$

# Contextual Centroid Prediction 

- bruh $$L_{cent}=\frac{1}{|\mathcal{F}_+|}\frac{1}{|\mathcal{S}_+|}\sum_i\sum_j (|\Delta\hat{c_{ij}}-\Delta c_{ij}|+|\hat{c_{ij}}-\overline{c_i}|)\cdot \mathbb{1}_S(p_{ij})$$
- where $$\overline{c_i}=\frac{1}{|S_+|}\sum_j\hat{c_{ij}}\quad,\quad\mathbb{1}_{S}(p_{ij}):\mathcal{P}\to\{0,1\}$$
- $|\mathcal{F}_+|$ =  the total number of GT boxes used to predict centroids
- $|\mathcal{S}_+|$ = number of points used to predict the instance center
- $\Delta\hat{c_{ij}}$ = offset predicted to instance center
- $\Delta{c_{ij}}$ = GT offset from a point $p_{ij}$ to center point 
- $\mathbb{1}_S$ = indicator fn to determine whether point is used 
	- implementation: expand GT box by some amount, then include all points inside the expanded box.

Examine the $|\hat{c_{ij}}-\overline{c_i}|$ term a bit more:
- Each GT box has one of these.
- For each relevant point (i.e. points inside the expanded GT box)
	- take their average, that becomes $\overline{c_i}$ 
- then while looping over each predicted centroid, the term is calculated. 
- overall effect seems to promote all assignment points inside a GT box to predict the same centroid location

