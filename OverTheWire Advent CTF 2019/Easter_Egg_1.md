
## OverTheWire Advent Bonanza 2019 — Easter Egg 1

![](https://cdn-images-1.medium.com/max/9856/1*ysIzi8PQiPZ0bMQ_jSyS9w.jpeg)

This write up is part of a bigger Capture The Flag competition. You can see other challenge write ups on the main post [here](https://medium.com/@forwardsecrecy/overthewire-advent-bonanza-2019-capture-the-flag-competition-66c50671c641).

Part of the fun of CTF challenges is searching for Easter Egg flags. These flags usually don’t require a ton of advanced skill, but are random fun things to find for points during an intense competition.

The only hint for Easter Egg 1 in the OverTheWire Advent Bonanza 2019 was:
>  TODO: make clean

Last year’s Easter Egg challenge revolved around the **robots.txt **file. **Robots.txt** does what it’s named for, and provides directions to web site crawlers and spiders (such as Google), as to what files it should and should not index. The challenge last year included the flag inside this file, just in case any end users or curious hackers looked at it’s contents. This year seemed a bit different though.

![](https://cdn-images-1.medium.com/max/2000/1*cDAqNjHa6pVuN_GTlWPGcQ.png)

No dice. The clue **TODO: make clean **refers to the C/C++ **make **command. Adding **clean** to this command tells the compiler to clean up any old temporary files and artifacts used during the build process. Using that knowledge, we can look for various types of these files on the web server. Eventually, we hit on **robots.txt~**, which indicates a temporary version of the **robots.txt **file.

![Finding the secret URL in the temporary robots.txt~ file](https://cdn-images-1.medium.com/max/2000/1*Ga5XYtlBJJzVZA6zoKdmLQ.png)

We can see from above, that there is a secret file named **/static/_m0r3_s3cret.txt** on the web server. Navigating to that file gives us the final flag, and the easter egg has been found.

![Sweet easter egg points!](https://cdn-images-1.medium.com/max/2000/1*YOPB_iW415YU1q6VUiFFnQ.png)

