
## OverTheWire Advent Bonanza 2019–Santa’s Signature

![](https://cdn-images-1.medium.com/max/12200/1*m7Jo2Tj0WFlug5e4MLo0mA.jpeg)

This write up is part of a bigger Capture The Flag competition. You can see other challenge write ups on the main post [here](https://medium.com/@forwardsecrecy/overthewire-advent-bonanza-2019-capture-the-flag-competition-66c50671c641).

### The Challenge
>  Can you forge Santa’s signature?

### The Data

We are provided a remote service to connect to, as well as some source code on how that service is running.

![The remote service](https://cdn-images-1.medium.com/max/2000/1*KJClFQt0PwzBRfs7Gx9dNA.png)

 <iframe src="https://medium.com/media/4bca5b5e2ef8b13cabac160466e94fa2" frameborder=0></iframe>

The remote service (and reading the source code) shows us a generated textbook **RSA** public key, and a request for us to provide 3 signed messages and digital signatures in hex encoding. Generally during CTF competitions, RSA challenges come down to factoring an unknown private key in order to decode a message. This is due to the fact that textbook RSA does not contain any padding, and can be defeated using cryptography and algebra.

![Textbook RSA](https://cdn-images-1.medium.com/max/4228/1*Rj73QYCxglnLKFHArhYybA.jpeg)

In order to crack the private key, we need a modulus (**n**) and exponent (**e**) that conform to certain properties (small modulus, small exponent, similar modulus/exponent)so that it’s easier to defeat the cryptography and math.

When checking these values, it seems we cannot crack the private key itself in this challenge due to such a large modulus (**n**) value.
>  n = 0xbb58dbdfd1999…[lots of characters]...d64f501c6640b95c57f
e = 65537

Based off the source code for the remote service, we need to pass 3 messages and provide 3 valid digital signatures per the *key.verify(m,s) *check*.*

Since we can provide the message and digital signature, there is an easy way to trick this automated verification system into accepting forged signatures. If we use the values of **0** for the message and signature, or the values of **1** for both — the RSA signature formula (s^e mod n) should still calculate out and pass all the requested checks.

![Passing check 1 and 2, but not check 3.](https://cdn-images-1.medium.com/max/2000/1*DNVDbUn8HH8WqXKyzLPxGQ.png)

We were able to pass message 1 and message 2 by using the values **0 **and **1**, but we can’t provide either of those again for the 3rd message. Bummer.

But wait…we know the modulus (**n**), the exponent (**e**), and we can control the digital signature (**s**). Using this, we can figure out an appropriate message (**m**) that should pass verification from a set digital signature.

 <iframe src="https://medium.com/media/8124bc697af82fc36989f2492ca2cb2b" frameborder=0></iframe>

Since this is an automated system, our message does not need to be human readable — it only needs to pass the signature verification check. Running the script above outputs a hex string that we can input as the message, and a digital signature of **0xf.**

![](https://cdn-images-1.medium.com/max/2000/1*P-Z4OqItEa6SJk92hb5xMw.png)

This passes the third check, and we can see the final flag.

