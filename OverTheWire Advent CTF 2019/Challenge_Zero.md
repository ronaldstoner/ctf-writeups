
## OverTheWire Advent Bonanza 2019 — Challenge Zero

This write up is part of a bigger Capture The Flag competition. You can see other challenge write ups on the main post [here](https://medium.com/@forwardsecrecy/overthewire-advent-bonanza-2019-capture-the-flag-competition-66c50671c641).

Prior to the start of OverTheWire Advent Bonanza 2019, the creators released a “Challenge Zero” for teams to work on. The challenge was located at [https://advent2019.overthewire.org/challenge-zero](https://advent2019.overthewire.org/challenge-zero), which showed a web page with an animated GIF of fire burning with the following message:

![](https://cdn-images-1.medium.com/max/2000/1*5fXFSfkeQVkqNjiW2mVvTA.gif)

    Fox! Fox! Burning bright! In the forests of the night!
    
    Hint: $ break *0x7c00

The above hint refers to the command line of **gdb**, a linux debugger. At this point though, we have nothing to break so we need to keep looking. In the spirit of Capture The Flag competitions, my team and I tried viewing the web page and GIF in different ways. Using the text based browser **links** leads us to our next clue.

![](https://cdn-images-1.medium.com/max/2000/1*8UX9wipYt9JvC0UuvVTajA.png)

The line “D0NT PU5H M3 C0Z 1M C1053 TO T3H 3DG3” is l33tspeak for a song lyric from *The Message* by **GrandMaster Flash**.

 <iframe src="https://medium.com/media/856e05837a79a6aad6b4d59b3f805115" frameborder=0></iframe>

The next lyric in the song “I’m trying not to lose my **HEAD” **clues us in that we need to make a **HEAD **web request. We can use **curl** and the command line to easily do this.

![Animated Texty Goodness](https://cdn-images-1.medium.com/max/2000/1*glBiQV_XktZHwXQFotHsmg.gif)

We can see in the above image that the flames are made up of random text characters to achieve the animation effect. The other hint we saw above was “If only the flames wouldn’t move so much” which alludes to the fact that the image is an animation made up of multiple frames. Since we are using **curl** on the command line, we can scroll back through our console buffer and see each frame of text making up the animation. I noticed that the string of text ended in “==”, which signifies **base64 **encoding. ****By compiling all the text and removing padding characters (# in this case), we get a completed **base64** string.

![Base64 decoding the string resulting in a new uuencoded file to play with](https://cdn-images-1.medium.com/max/2000/1*ZExMnIz3uub3zek9u632pA.png)

The output above is uuencoded and can be decoded using the xxd tool. Once decoded, we have a boot.bin file. To my surprise, the **base64** string did not contain the flag itself, but rather a bootable virtual machine image.

![Confirming the file type of boot.bin](https://cdn-images-1.medium.com/max/2000/1*Rid9lSRDA8e81J_vzWtl7Q.png)

Taking the binary boot file and loading it into a virtualization hypervisor resulted in the following:

![](https://cdn-images-1.medium.com/max/2000/1*tCnOxFvVvdRPzWO7uQmGzg.png)

Aha! We have a binary that is loaded and referencing last year’s CTF challenge using the **RC4** stream cipher. It seems we need to break this binary as well. Thankfully, the linux command line debugging tool **gdb **can connect to remotely running binaries for remote debugging purposes. Our original hint of *break *0x7c00* finally comes into play. We can now load up **gdb**, set the proper breakpoint, and start attacking the binary.

Using **gdb** allows us to dump the Intel formatted assembly code so we can get a better understand of what is going on.

![A sampling of the dumped code](https://cdn-images-1.medium.com/max/2000/1*ZocSjgylPVnt-n5jO2wc2Q.png)

We can see from the code that we are performing some **AES** encryption functions of data in the registers. I also noticed that there was a condition to check if the input was 16 characters or not. If it wasn’t, a different jump and code routine was executed. When a password of 16 characters is used, a new jump is taken which performs some XOR operations on the code and various registers.

The program ultimately takes the users input and stores it into **xmm3**. The instruction at **0x7c62: movaps xmm0,XMMWORD PTR [rsi]** moves the AES encryption key into **xmm0**.

![Storing the user input into xmm3](https://cdn-images-1.medium.com/max/2000/1*oRZ_w71dw5TiZb7pL9RrSg.png)

![The key is loaded into xmm0](https://cdn-images-1.medium.com/max/2000/1*74j04SrTedlskgpQAdDQaQ.png)

The data in **xmm3** then gets XOR’d against a static address which contains hard coded cipher text to see if it matches. If it does, we get the flag. If it does not, the program cleans up the registers and prompts the user again for a password. When we check the debug output we can find the hard coded location and it’s data.

![The location of the hard coded cipher text](https://cdn-images-1.medium.com/max/2000/1*dHDZ0-1_3VTRJxEu7wc0bg.png)

![The cipher text contents in little endian format](https://cdn-images-1.medium.com/max/2000/1*YgGmIC5I2iqGTf4ihAjcwg.png)

Taking both the cipher text and key allows us to perform an AES decryption which reveals the password we need — **MiLiT4RyGr4d3MbR**.

{% gist b9cb4330085e17f8dad1d723f732ee2c }

![Running the script and decrypting the password](https://cdn-images-1.medium.com/max/2000/1*tTmrmNJ_i9xvRmkzxZ1iyg.png)

At this point we can input the password into the virtual machine, pass the check, and receive the final flag. We went from an animated gif, to base64 text, to a uuencoded boot image, to a binary that needed to be remotely debugged. What a challenge!

![](https://cdn-images-1.medium.com/max/2000/1*Gt9cUDLfkhy7zYfEnIAO9A.png)

