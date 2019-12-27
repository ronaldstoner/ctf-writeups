
## Army Cyber Command — Cipher Challenge

![](https://cdn-images-1.medium.com/max/2000/1*a8k8cpExVuJ41SAJJjc4Eg.png)

Back in 2016, the Cyber Command branch of the United States Army (ARCYBER) released a commercial that lead to an ARG for recruitment of hackers. A YouTube video ([https://www.youtube.com/watch?v=0LZnOorfS_Q)](https://www.youtube.com/watch?v=0LZnOorfS_Q) of the commercial showed the URL [http://recruitahacker.net/](http://recruitahacker.net/) in the console lines displayed in the video. Browsing to this URL opened up puzzles for those willing to test their skills (and possibly set themselves up for recruitment).

![Easter eggs in the YouTube commercial](https://cdn-images-1.medium.com/max/2000/1*oJBL38ecnx10QJgKQ6Kezw.png)

In the present day the puzzle and cipher text has changed a few times due to previous solutions being published. While I’m not sure if the site is still being used for recruitment, the challenge itself is pretty fun.

Going to the **/puzzle** URL on the website shows a challenge to break a simple cipher algorithm with the cipher text of “**Ccoheal ieuw wqu tcb**”.

![Browsing to the puzzle URL and seeing the cipher challenge](https://cdn-images-1.medium.com/max/2000/1*vBxhI147stmx-ES647NEhA.png)

Using the same cipher text as the encryption key returns an output consisting all of the character “**a**”. This is important as it can give some clues as to what cipher is being used.

![Decrypted text filled with the “a” character](https://cdn-images-1.medium.com/max/2000/1*-uFjA5e17A7q7ofTUlTBhg.png)

After doing some research and trying a few things it’s determined that a **Vigenère cipher** ([https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)) is being used in this particular challenge. In order to understand what a Vigenère cipher does, we must first understand what a Caesar cipher is.

In a Caesar cipher a base number is used to shift every letter of the Alphabet. A Caesar cipher with a shift of 5would result in the letter **A** being changed to **F**, the letter **B** being changed to **G**, and so on.

Vigenère takes the concept of Caesar ciphers and sequences them using different shifts, resulting in a **Vigenère square** which can be used for encryption and decryption of text.

![An example Vigenère square](https://cdn-images-1.medium.com/max/2000/1*q28PGoM7RXhzO2vACdJGNQ.png)

Taking what we know from above we can programmatically start to solve the challenge. The coding language **Python** can be used to determine the shift values used, and even generate our own decryption keys and encrypted text if we’d like.

Script: https://gist.github.com/ronaldstoner/24f234a9b2f896e645ae885dd818ee95#file-ciphertext-py

Running the code above generates a decryption key of **“lkvtrwu xaqd jwx fqv”**. Putting in this decryption key allows the text to be decrypted and now we have full control over the plain text and encryption key.

![Decrypting the text resulting in a fun message](https://cdn-images-1.medium.com/max/2000/1*ikz3f4vfWTvqTIFrWwJdWw.png)

As seen above the text did decrypt correctly using the key we found from the Python script. While we have owned the cipher and challenge, the message *“Sorry, that is not the correct solution”* still appears. I’m not sure if ARCYBER is still accepting entries for recruitment on this particular web challenge, but the “correct” solution seems to be changing all the time. I’ve seen a few examples where others have solved this using different decrypted text. While we may not have “solved” the challenge itself, we’ve taken control of the keys, plain text, and can make it now show whatever we’d like.

Side Note: Enumerating through this challenge found other URLs such as [http://recruitahacker.net/Home/XOREncryptionTest](http://recruitahacker.net/Home/XOREncryptionTest) and [http://recruitahacker.net/Home/VigenereTest](http://recruitahacker.net/Home/VigenereTest), which are used to encrypt and test plain text strings. I’m sure there are more hidden easter eggs on this site to be found and I look forward to any future updates and challenges.

![](https://cdn-images-1.medium.com/max/2000/1*5XoOFVhjjVaFFfX9v7T9Jg.png)

*Editors Note: Put a [WEBGAP](https://webgap.io) between you and the malware with a [browser isolation technology](https://webgap.io/remote-browser-isolation-technology.html) or by leveraging a [remote browser service](https://webgap.io/remote-browser-service.html).*

