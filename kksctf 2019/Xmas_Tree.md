
## #kksctf open 2019 — Xmas Tree

![](https://cdn-images-1.medium.com/max/14400/1*MuK8_pkbWJ6IKV6RBivkOA.jpeg)

## The Challenge
>  Do you like to decorate the Christmas tree?

This challenge was listed as “Misc”, and no other hints were provided.

## The Solution

This was an easy challenge, as the answer was literally staring participants in the face during the entire CTF. Navigating to the kksctf web page showed a variety of Christmas themes, including a neat **ASCII** Christmas tree.

![ASCII Art!](https://cdn-images-1.medium.com/max/2000/1*UvaTSqSaJ-61cl7EmFpivg.png)

The above tree **ASCII** art shows a few different pieces of text in different colors. If we look in the HTML source, we can see the **<span>** tags which indicate a color change for certain pieces of text.

![It looks sweet in HTML too!](https://cdn-images-1.medium.com/max/2000/1*tqbqgwAlOSChtwGXwH2Ulg.png)

Combining the 7 pieces of colored text results in the final flag of **kks{n3w_y34r_m@dn3$$}**. Happy New Year!

