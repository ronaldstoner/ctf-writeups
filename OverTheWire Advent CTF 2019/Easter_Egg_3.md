
## OverTheWire Advent Bonanza 2019 — Easter Egg 3

![](https://cdn-images-1.medium.com/max/11304/1*9AgNRjrqDOwjZGWZMAfERA.jpeg)

This write up is part of a bigger Capture The Flag competition. You can see other challenge write ups on the main post [here](https://medium.com/@forwardsecrecy/overthewire-advent-bonanza-2019-capture-the-flag-competition-66c50671c641).

Part of the fun of CTF challenges is searching for Easter Egg flags. These flags usually don’t require a ton of advanced skill, but are random fun things to find for points during an intense competition.

Easter Egg 3 directed competitors to the OverTheWireCTF Twitter page located at [https://twitter.com/OverTheWireCTF](https://twitter.com/OverTheWireCTF). One thing that jumped out was a post with a picture signifying a new day in the competition.

![Do you see what I see?](https://cdn-images-1.medium.com/max/2400/1*7k5TnyDvMk6ohHyWvrsh2A.jpeg)

The above image actually has a ton of information in it. On first glance, we can see a QR code in the middle of the Egg wearing the Santa hat. Scanning the QR code may yield different results depending on the end user and the application. Here’s why.

Everyone is used to a few types of barcode formats such as QR, PDF417 (on the back of US licenses), and UPC/EAN for products they purchase.

![[https://support.route4me.com/barcode-types-and-scanning-speed-of-the-route4me-android-app/](https://support.route4me.com/barcode-types-and-scanning-speed-of-the-route4me-android-app/)](https://cdn-images-1.medium.com/max/5120/1*f5uN6OTQyEU13BFLbHnIaQ.png)

Most people are used to scanning a single type of code and getting the information they need. The above challenge is interesting in that the image pictured contains both a QR code, AND an Aztec formatted code (right in the middle of the picture). If we look back at the original image on Twitter, we can see that the font used in “DAY 10” appears to be Aztec in nature…oh well, let’s scan some codes!

## Scan Results

### Aztec: 414f54577b6234726330643373

### QR Code: 137:64:137:154:171:146:63:175

The numbers in the Aztec code portion correspond to ASCII characters in Hex notation. An example of this would be 41 representing the letter “A”. Decoding this section gives us the first part of the final flag.

**AOTW{b4rc0d3s**

The numbers above in the QR code portion correspond to ASCII characters in Octal notation. For example, the number 137 represents the character “_”. Decoding this section gives us the second part of the final flag.

**_4_lyf3}**

Combining both parts yields us the completed and final flag.
>  **AOTW{b4rc0d3s_4_lyf3}**

