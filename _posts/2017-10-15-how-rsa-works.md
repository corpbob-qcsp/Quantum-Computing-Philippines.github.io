---
layout: post
title:  "How RSA Encryption Works"
author: bobby
image: assets/images/encryption_2.jpeg
categories: Cryptography
comments: false
---

Alice and Bob live in different parts of the world. They want to communicate with each other but they don't want anyone to know the messages they exchange. In order to protect the message, they need to encrypt their message. Bob comes up with a secret key that will allow both of them to encrypt and decrypt their messages so that when they send it via email they will be confident that no one can read the message if ever someone (like Eve) intercepted the message. However, they have a dilemma. How will Bob send the encryption key to Alice ? If he sends the key and Eve intercepts it, then Eve will be able to decrypt the messages both Alice and Bob exchange and know what they are up to. 

Bob should be able to send the key to Alice encrypted so that Eve will not be able to read it. In order to do this, Bob will have to create a second key to encrypt the key he wants to send to Alice. The problem now is how will Alice decrypt the message (which is the encrypted key) if she does not have the second key?

This is where RSA encryption is used. Suppose we want to encrypt a message using RSA, what we'll do is find 2 large prime numbers p and q and get their product N = pq. We will need another number e, which we will use to encode the message into a ciphertext. The set of numbers N and e is called the <strong>public key</strong> which Alice can send to Bob via email. Bob will use these numbers to encrypt the secret key before sending to Alice.

We will represent a textual message like "THIS IS A SECRET MESSAGE" into numbers. To accomplish this, we need to map letters into numbers like the following:

$$
\begin{array}{|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|}
\hline
A & B & C & D & E & F & G & H & I & J & K & L & M & N & O & P & Q & R & S & T & U & V & W & X & Y & Z & \\\hline
0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10& 11& 12& 13& 14& 15& 16& 17& 18& 19& 20& 21& 22& 23& 24& 25& 26\\
\hline
\end{array}
$$

Using the above mapping, we can write 'THIS IS A SECRET MESSAGE' as:

19 7 8 18 26 8 18 26 0 26 18 4 2 17 4 19 26 12 4 18 18 0 6 4

If a number is less than 10, we pad it with a zero to the left. The message then becomes:

19 07 08 18 26 08 18 26 00 26 18 04 02 17 04 19 26 12 04 18 18 00 06 04

To conserve some space, we can group the numbers into groups of 4:

1907 0818 2608 1826 0026 1804 0217 0419 2612 0418 1800 0604

Now for each $M$ number above, we will encode it using the formula:

$$
\displaystyle C = M^e \mod N

$$



<blockquote>What is this mod operation? The above says that we raise M to the exponent e, divide the result by N and get the remainder. For example, if M=10, e=7 and N=17 we have

$$
10^7=10000000
$$

Now divide the result by 17 and get the remainder:

$$
10000000 / 7 = 588235 \text { Remainder } 5
$$

Therefore

$$
10^7 \mod 17 = 5
$$

In python we can get the answer using the <code>pow</code> function:

{% highlight python %}
>>> pow(10,7,17)
5
{% endhighlight %}

</blockquote>

Let's say we choose p = 53, q=59 and e = 7. This gives us $N=pq = 53\times 59 = 3127$. To encode 1907, we do

$$
\displaystyle C=1907^7 \mod 3127 = 0794
$$


The number 0794 is now the ciphertext. It is the number we give to the recipient of the message. We can use python to generate the ciphertext above.

{% highlight python %}
for n in ("1907","0818","2608","1826","0026","1804","0217","0419","2612","0418","1800","0604"):
   print pow(int(n),7,3127)                                                                
{% endhighlight %}

Doing this for all numbers we get:

0794 1832 1403 2474 1231 1453 0268 2223 0678 0540 0773 1095

When the recipient gets this message, she can decipher it using a key which she keeps private to herself. The key d is the inverse of e modulo $(p-1)(q-1)$. The key d is called the <strong>private key</strong>. We can retrieve the original message using the formula:

$$
\displaystyle M = C^d
$$

The number d can be calculated using the following formula:

$$
ed \mod (p-1)(q-1) \equiv 1
$$

Using python we can compute for d using the following program:

{% highlight python %}
>>> p=53
>>> q=59
>>> e=7
>>> NN = (p-1)*(q-1)
>>> for d in range(1,NN):
...   x = e*d % NN
...   if x == 1:
...     print 'd = ', d
... 
d =  431
{% endhighlight %}

Using d = 431 and applying the decipher formula to the first block, we get 

$$
\displaystyle M = 0794^{431} \mod 3127 = 1907
$$

which is our original message!

We now apply this to the entire ciphertext 

0794 1832 1403 2474 1231 1453 0268 2223 0678 0540 0773 1095 

using the python program below:

{% highlight python %}
for n in ("0794","1832","1403","2474","1231","1453","0268","2223","0678","0540","0773","1095"):
   print pow(int(n),431,N)
{% endhighlight %}

we get 

1907 0818 2608 1826 0026 1804 0217 0419 2612 0418 1800 0604

which is our original full message! It's just a matter of mapping these numbers back to letters to get the message text.

Using this mechanism, Alice will send 2 numbers N and e to Bob which he will use to encrypt the secret key and send to Alice. When Alice receives the encrypted secret key, she will use her private key d to decrypt it and get the secret key. After that, they can now start using the secret key to encrypt messages between them.


Image Credit: "Kryha-Chiffriermaschine, Kryha-Encryption Device" by Ryan Somma is marked with CC BY 2.0. To view the terms, visit https://creativecommons.org/licenses/by/2.0/?ref=openverse
