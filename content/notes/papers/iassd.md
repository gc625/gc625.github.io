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