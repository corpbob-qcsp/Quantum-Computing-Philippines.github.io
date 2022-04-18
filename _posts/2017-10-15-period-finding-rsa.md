---
layout: post
title:  "Period Finding and the RSA"
author: bobby
image: assets/images/period_finding.jpeg
categories: Cryptography
comments: false
---

In the previous post, we learned how to <a href="/2017-10-15/how-rsa-works" rel="noopener" target="_blank">decrypt RSA</a> by getting the factors of the big number N and computing for the inverse of e (the encoding number) modulo N. There is also another way to decrypt an RSA encrypted message. This is when you are able to get the period of the ciphertext. If c is the ciphertext, the <strong>period</strong> r is the smallest integer that satisfies:

$$
c^r \equiv 1 \mod N
$$

Once we get the period, we compute for $d^\prime $, the inverse of e modulo r:

$$
ed^\prime \equiv 1 \mod r
$$

The inverse can then be used to decrypt the ciphertext:

$$
m=c^{d^\prime}
$$

 
In our previous example, we encrypted the message 

THIS IS A SECRET MESSAGE 

using public key p=53, q=59, N=pq=3127 and e=7 and private key d=431. The "plain text" is

1907 0818 2608 1826 0026 1804 0217 0419 2612 0418 1800 0604

and the ciphertext is:

0794 1832 1403 2474 1231 1453 0268 2223 0678 0540 0773 1095

Let's compute the period of the first block of our ciphertext:

$$
0794^r \equiv 1 \mod 3127
$$

Using the python script below, we can compute the period

[code language="python"]
for r in range(1,100):
   p=pow(794,r,N)
   if p == 1:
     print "%d %d" % (r,p)
[/code]

The result of running the above program gives r=58. We can then compute $d^\prime $ using the following equation:

$$
ed^\prime = m\times r + 1
$$

The above equation is satisfied when m=3 and $d^\prime = 25 $. Using this value of $d^\prime $, we can compute for 

$$
\begin{array}{rl}
m&=0794^{25} \mod 3127 \\
&= 1907
\end{array}
$$

which gives us the original message!

However, unlike using the private key, you need to compute the period r and $d^\prime $ for every block of the ciphertext (unless the ciphertext is composed of only one block). However, that should not stop a cracker from deciphering all the blocks.


Image Credit: "R-O-B/Structural Oscillations" by NYCDOT is marked with CC BY-NC-ND 2.0. To view the terms, visit https://creativecommons.org/licenses/by-nd-nc/2.0/jp/?ref=openverse
