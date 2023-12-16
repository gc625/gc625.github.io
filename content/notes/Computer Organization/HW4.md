
---
title: "HW4"
enableToc: true
---


# Q2 

cfg	| mean access time (T_c) |	Cache access time (c) |Main memory access time (m)	| Hit ratio (h,%) 
--|-|-| -|-
A|	26 |	10 |80 | 80
B|21|15|60|90
C|23|8|60|75
B, since it has the shortest mean access time.


$H_1= 0.9$
$H_2=0.8$
$C_1=0.5C_2$
$C_2=0.1M$
$M$ = main memory access time

$T_{avg} =  C_1 + (1-H_1)(C_2 + (1-H_2)M)$
$T_{avg} = 0.5C_2 + 0.1(C_2+0.2M)$
$T_{avg}=0.6C_2+0.02M$
$T_{avg}=0.06M+0.02M=0.08M$

$\frac{1}{0.08}=12.5\implies$ two lvl system is 12.5 times faster 

# Q3
setup|effective disk size (TB)|per tb cost|failure tolerance
-| -| - | - 
RAID 0	|96|$18.75|	0, basically one big hard drive, whole thing dies if one fails
RAID 1|	8|$225.00|11, 11 drives are just copies, so they can all die
RAID 1+0|16|$112.50|5, we have 2 sets of 6 drives (RAID1), so up to 5 can die in a RAID1 cfg
RAID 100 |32| $56.25 | 2, 4 sets of 3 drives in RAID1, so 2 can die 
RAID 5|	88	|$20.45|1, just one parity disk
RAID 5+0 |80|$22.50|1, also just one parity disk
RAID 6	|80|$22.50|	2, two parity disks
RAID 6+0 |64|$28.13	|2, also two parity disks


# Q4 
| $b_3$ | $b_2$ | $b_1$ | $b_0$ | f |
|-------|-------|-------|-------|---|
|   0   |   0   |   0   |   0   | 0 |
|   0   |   0   |   0   |   1   | 1 |
|   0   |   0   |   1   |   0   | 0 |
|   0   |   0   |   1   |   1   | 1 |
|   0   |   1   |   0   |   0   | 0 |
|   0   |   1   |   0   |   1   | 1 |
|   0   |   1   |   1   |   0   | 0 |
|   0   |   1   |   1   |   1   | 1 |
|   1   |   0   |   0   |   0   | 0 |
|   1   |   0   |   0   |   1   | 0 |
|   1   |   0   |   1   |   0   | 0 |
|   1   |   0   |   1   |   1   | 0 |
|   1   |   1   |   0   |   0   | 0 |
|   1   |   1   |   0   |   1   | 0 |
|   1   |   1   |   1   |   0   | 0 |
|   1   |   1   |   1   |   1   | 0 |
## b)
$f=b_3'b_2'b_1'b_0+b_3'b_2'b_1b_0+b_3'b_2b_1'b_0+b_3'b_2b_1b_0$
![[logic gate.jpeg]]
## c) 
$f=b_3'b_2'b_1'b_0+b_3'b_2'b_1b_0+b_3'b_2b_1'b_0+b_3'b_2b_1b_0$
$=b_0b'_3(b_2'(b_1+b_1')+b_2(b_1+b_1'))$
$=b_0b_3'$
![[Pasted image 20221024222420.png]]
## d)
$g= (b_2'b_3')(b_3'b_0)$
$= b_0b_2'b_3'$
![[Pasted image 20221024222630.png]]



# Q5 

i) 
f 
| x | y | z | y'z | z' | yz' | y'z+z'+yz' | x(y'z+z'+yz') | z'+x' | y'(x'+z') | x(y'z + z' + yz') + y'(z' + x') |
|:-:|:-:|:-:|-----|----|-----|------------|---------------|-------|-----------|---------------------------------|
| 0 | 0 | 0 |   0 |  1 |   0 |          1 |             0 |     1 |         1 |                               1 |
| 0 | 0 | 1 |   1 |  0 |   0 |          1 |             0 |     1 |         1 |                               1 |
| 0 | 1 | 0 |   0 |  1 |   1 |          1 |             0 |     1 |         0 |                               0 |
| 0 | 1 | 1 |   0 |  0 |   0 |          0 |             0 |     1 |         0 |                               0 |
| 1 | 0 | 0 |   0 |  1 |   0 |          1 |             1 |     1 |         1 |                               1 |
| 1 | 0 | 1 |   1 |  0 |   0 |          1 |             1 |     0 |         1 |                               1 |
| 1 | 1 | 0 |   0 |  1 |   1 |          1 |             1 |     1 |         0 |                               1 |
| 1 | 1 | 1 |   0 |  0 |   0 |          0 |             0 |     0 |         0 |                               0 |

g 
![[Screenshot 2022-10-24 at 11.08.48 PM.png]]

# ii)

$$\begin{align}f =&\ xy'z + xz'+xyz' + y'z' + y'x'\\ 
=&\ y'(xz+x')+z'(x+xy+y') \\ 
=&\ y'((x+x')(x'+z))+z'(x+y')\\
=&\ y'(x'+z)+z'(x+y')\\
=&\ y'x'+y'z+z'x+y'z'\\
=&\ y'(z'+z)+y'x'+z'x \\
=&\ y'+y'x'+z'x\\
=&\ y'+z'x\end{align}$$
$$\begin{align}g=&\ x'y'z' + z(x'w'y' + x'y + wy') + wy'z'\\
=&\ x'y'z'+z(x'(w'y'+y)+wy')+wy'z'\\
=&\ x'y'z'+z(x'(w'+y)+wy')+wy'z'\\
=&\ x'y'z'+zx'(w'+y)+wy'(z+z')\\
=&\ x'y'z'+zx'w'+zx'w+wy'\end{align}$$

## iii)

| x | y | z | y'+z'x |
|:-:|:-:|:-:|:------:|
| 0 | 0 | 0 |    1   |
| 0 | 0 | 1 |    1   |
| 0 | 1 | 0 |    0   |
| 0 | 1 | 1 |    0   |
| 1 | 0 | 0 |    1   |
| 1 | 0 | 1 |    1   |
| 1 | 1 | 0 |    1   |
| 1 | 1 | 1 |    0   |
which matches
| w | x | y | z | x'y'z' | zx'w' | zx'w | wy' | g |
|---|---|---|---|--------|-------|------|-----|---|
| 0 | 0 | 0 | 0 | 1      | 0     | 0    | 0   | 1 |
| 0 | 0 | 0 | 1 | 0      | 1     | 0    | 0   | 1 |
| 0 | 0 | 1 | 0 | 0      | 0     | 0    | 0   | 0 |
| 0 | 0 | 1 | 1 | 0      | 1     | 0    | 0   | 1 |
| 0 | 1 | 0 | 0 | 0      | 0     | 0    | 0   | 0 |
| 0 | 1 | 0 | 1 | 0      | 0     | 0    | 0   | 0 |
| 0 | 1 | 1 | 0 | 0      | 0     | 0    | 0   | 0 |
| 0 | 1 | 1 | 1 | 0      | 0     | 0    | 0   | 0 |
| 1 | 0 | 0 | 0 | 1      | 0     | 0    | 1   | 1 |
| 1 | 0 | 0 | 1 | 0      | 0     | 1    | 1   | 1 |
| 1 | 0 | 1 | 0 | 0      | 0     | 0    | 0   | 0 |
| 1 | 0 | 1 | 1 | 0      | 0     | 1    | 0   | 1 |
| 1 | 1 | 0 | 0 | 0      | 0     | 0    | 1   | 1 |
| 1 | 1 | 0 | 1 | 0      | 0     | 0    | 1   | 1 |
| 1 | 1 | 1 | 0 | 0      | 0     | 0    | 0   | 0 |
| 1 | 1 | 1 | 1 | 0      | 0     | 0    | 0   | 0 |


which matches.