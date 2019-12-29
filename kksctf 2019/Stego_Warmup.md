
## #kksctf open 2019 — Stego Warmup

![](https://cdn-images-1.medium.com/max/2000/1*1mWChuZbCKOtnXFq5_A7kg.jpeg)

## The Challenge
>  We get some file. Can you find secret?

We are provided the above file of Shaq gracefully obfuscating himself behind a tree. No other clues or hints are provided.

## The Solution

This was an extremely easy steganography challenge. The flag we are looking for is embedded inside the image data. We can extract the **EXIF** metadata from the image using **exiftool**, revealing the final flag.

![Solved!](https://cdn-images-1.medium.com/max/2000/1*4QMDWgZMOs38BhXuPd1AbQ.png)

We can see in the above image that the flag was in the **Author** field in clear text. On to the next challenge!

