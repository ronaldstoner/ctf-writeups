
## #kksctf open 2019 — Red XOXOXO

![](https://cdn-images-1.medium.com/max/3000/1*D-utnHa5XIelU-b76t_A-g.jpeg)

## The Challenge

We receive a message that is captured, and since this challenge is listed as “crypto”, we need to decipher the cipher text of *-*;91~.,1*1=12~;-*?<27–6;:r~+-;~=27;0*~*1~=100;=*p~7y3~)?7*709~81,~+,~,;.2'p~55-%?**j=5.?*.:j)0#*

![Our challenge information](https://cdn-images-1.medium.com/max/2000/1*tZL91dB62b7Jg_SeVc-6xw.png)

## The Solution

The above cipher text has a variety of characters in it. Due to this, we can greatly reduce the type of encryption being used. Our hint also gives us a pointer in the right direction with the **XOXOXO **(XOR) in the title.

![Brute forcing the key space](https://cdn-images-1.medium.com/max/2000/1*r9VYUQ8Iy6DF3C72H8lrDw.png)

When attempting a variety of ciphers, a **XOR** brute force attack is attempted.

![Finding a possible key](https://cdn-images-1.medium.com/max/2000/1*RmzSTDk48cYsg3x-wb14eg.png)

Our brute force attack has found a possible key and provided us with positive confirmation in the form of clear text. Unfortunately, this is not the correct flag, as this tool and key combination do not give us correct output. Using another tool, we can brute force the key space and find the correct key.

![Finding the flag for the win](https://cdn-images-1.medium.com/max/2000/1*thrbER_5n84iOXZHlOEAog.png)

The key of 5e is found and the string is decrypted, revealing to us the final flag.

