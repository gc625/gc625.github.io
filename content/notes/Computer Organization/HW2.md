---
title: "HW2"
enableToc: true
---


# Q2

**70%** Type A: register-to-register, 10 clock cycles 
**30%** Type B: read/write, 110 clock cycles to read or write once both the instruction
and the operand have been fetched.

## A
Type A: total time $= 110 + 10 = 120\text{ns}$
Type B: total time $= 110 + 110 + 110 = 330\text{ns}$

Average  $0.7\cdot 120 + 0.3\cdot 330= 183$ clock cycles

## B
$T =\frac{1}{200\text{MHz}} = 5\text{ns}$ 
so average execution time = $5\cdot 183 = 915 \text{ ns}$ 

## C
so $\frac{1000}{915\text{ns}} = 1.093 \text{MIPS}$ 

## D 
If instead $f=1\text{GHz}$, then $T= 1\text{ns}$ which impleis instruction time is $183\text{ ns}$
$\frac{1000}{183\text{ns}} = 5.46 \text{MIPS}$

## E
Then $\frac{20MI}{5.46MIS^{-1}}=3.66 s$


# Q3 

## A
so sequential operation, cycle time is $4+14=18\text{ ns}$. Since it takes 3 clock cycles, instruction time = $18\cdot 3 = 54 \text{ns}$. Therefore $\frac{1000}{54} = 18.519 \text{MIPS}$
$f= 55.555 MHz$


## B 
If its pipelined, then an instruction completes every cycle, which implies frequency = bandwidth  =  55 MIPS

## C 
for part A:  $\frac{2 M}{18.519}=0.108 s$
for part B:  $\frac{2 M}{55}=0.0364s$



# Q4 

System | Cycle Time | Clock Frequency | Instruction Completion Time | BW
-- | -- | -- | -- | --
A | 4 ns | 250 MHz | 20ns | 250 MIPS
B | 5 ns | 200 MHz | 25ns | 200 MIPS
C | 5 ns | 200 MHz | 25ns | 40 MIPS
D | 1 ns | 1 GHz | 10ns | 1000 MIPS
E | 6 ns | 166.66 MHz | 36ns | 166.66 MIPS
F | 1.5 ns | 666 MHz | 15ns | 66 MIPS
System D is the fastest because it has the highest bandwidth (1000MIPS), and therefore it processes the most number of instructions per second. 

## Calculations 

A: 250 MIPS for pipelined $\implies f=250MHz$.
So $T=1/f = 4ns$. 
Therefore Instruction completion time (latency) is $5T = 20 ns$ 

B:  5 stage + cycle time = 5ns $\implies$ latency = $25ns$. Clock freq is $1/5ns$, BW is same but in MIPS

C: no pipline latency = 25ns  $\implies$ bandwidth = $1000/25ns =40 MIPS$
cycle time = 5 ns  $\implies$ frequency = $1/5ns = 200$ Mhz 

D: Pipelined, latency = 10 ns , f = 1GHz 
so T = $1/1GHz = 1ns$ 
so  BW = 1000 MIPS 
and latency = $1\cdot 10 = 10ns$ 

E : 6 pipeline, latency = $36 ns$ implies $T=6ns$ and $f= 1/6ns = 166.66 MHz$ BW is same but in MIPs becuase pipeline

F: No pipeline, latency = 15ns $\implies BW = 66MIPS$   
$f= 666.6MHz \implies T=1.5ns$




# Q5

. | 00101101 | 10010101
-- | -- | -- 
Pure Binary | 1+4+8+32 = 45  | 1+4+16+128=149
Sign-magnitude | MSB=0 so 45 | MSB=1 so -(1+4+16)= -21
Two's Complement |MSB=0 so 45 | MSB=1 so -128+21=-107
Excess 128 | 45+128=163, 163 in binary = 10100011 | not possible with $k=8$
BCD | 0010 = 2, 1101 = 13, so not valid | 1001 = 9, 0101 = 5 so 95
hexadecimal |0010 = 2 1101 = D | also 95 since neither digit >9 


# Q6

Original Number | Binary Number in Register | Value as interpreted with the new representation
-- | -- | -- 
10 pure bin k=4 | 1010 | 10-8 = 2 
-11 sign-magnitude k=5 | 11011 | -16+(11) = -5
48 pure bin k=6| 110000| -32+(16) = -16
-27 2s k=6| 100101| 1+4+32 = 37
-76 2s k=8  | 10110100 | B4
38 2d bcd| 00111000 | MSD=0 so just 8+16+32 = 56
6DH 2D hex | 01101101 | 109-128 = -18 
