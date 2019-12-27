
## Brain_Gamez — A Forensic Challenge

Back in October I created a forensic challenge called Brain_Gamez, which was hosted on the Hackmethod Monthly CTF site ([https://ctf.hackmethod.com](https://ctf.hackmethod.com/)). Brain_Gamez is a 32 KB JPG image file, which contains a mix of steganography and obfuscation techniques. It was up to the CTF hackers to figure out what to do with the image, and piece together a solution to find the final flag. What follows is the steps taken needed to solve the challenge.

The challenge starts below:
> # “Our former employee Kevin is a pretty BRAINy guy. We found this image on his employee laptop. We think there’s something there. Can you help us figure out if our suspicions are correct?”

Link to file: [https://www.dropbox.com/s/f322lg388dy065j/brain.jpg?dl=0](https://www.dropbox.com/s/f322lg388dy065j/brain.jpg?dl=0)

Those that may not be familiar with forensic techniques and steganography tools may be lost at this point. In order to work through a challenge like this, you first must get an idea of what you’re looking at. Most digital pictures store metadata (EXIF) containing information about the camera type, date/time, and photographer - so we should look there first.

![](https://cdn-images-1.medium.com/max/2000/1*coJWoKIWtQgxDtrvDqeoHQ.png)

Running exiftool brain.jpg show a list of metadata associated with the image. If you look at the “Image Description” line in the EXIF data, you’ll see what appears to be a base64 encoded string — strange.

![](https://cdn-images-1.medium.com/max/2000/1*rkwxjcFYNamCWbnxC1mpAw.png)

Base64 decoding the string reveals the plain text “N0t_F1aG_BuT_m4Y_n33d". So, now what? We’ve decoded the base64 string found in the EXIF data, but we don’t know where the string is needed. Let’s dive deeper into the image. The **file** command should show us if the image is indeed what it says it is.

![](https://cdn-images-1.medium.com/max/2000/1*J48Bn9bzGDQZl6P22ZW57g.png)

**File** command output looks normal for the most part. We see the **base64** string listed in the command output, so the string above that we decoded could have been found that way as well. Time to look further.

![Viewing the hex for the start of the file](https://cdn-images-1.medium.com/max/2000/1*frN2NrAzAY5VrWcJps71vA.png)

![Viewing the hex for the end of the file](https://cdn-images-1.medium.com/max/2000/1*CIc68WU-EsZZ1pTtukXQBg.png)

Hex editing the JPEG file shows the expected JPEG file headers, with the hex values FF D8 (Start of Image)listed. What’s unexpected though, is the end characters being different than FF D9 (End of Image) and the text flag.txt showing up.

![](https://cdn-images-1.medium.com/max/2000/1*GMLPmoEo10e4DtHRaBDcow.png)

Using the **binwalk** command to confirm our suspicions, we see that there is a compressed zip archive inside the image. We could use **binwalk** to extract the zip file itself, but let’s try the easier path first.

![](https://cdn-images-1.medium.com/max/2000/1*LiLuHoD67bX_qWizBjV8bA.png)

Unzipping the file prompts us for a password. Entering the password we retrieved before N0t_F1aG_BuT_m4Y_n33d succeeds and the file **flag.txt** is extracted. Revealing the contents of **flag.txt** shows the following:

![](https://cdn-images-1.medium.com/max/2000/1*b2x8X3bvi411vaiTCezw_A.png)

A-ha! So Kevin the ex-employee **was** up to nefarious doings! It seems he’s passing some information to a hacker named Condor. The message content indicates we need to go deeper if we are to find what we’re looking for. At the end of the message we also see the text Lay3rz_0f_Obfusc4t10n. This looks similar to a password, don’t you think?

How else could we go deeper? We’ve checked the image metadata, hex dumped the file contents, and extracted a hidden zip archive. We’ve looked at the start of the file, and the end of the file, but not the middle. If there’s more data there we should be able to extract it. Enter **steghide**.

![](https://cdn-images-1.medium.com/max/2000/1*immeJI3gR487Qjf6KypT6Q.png)

Steghide seems to see something, and is prompting us for a password. Using the text Lay3rz_0f_Obfusc4t10n from the previous communication extracts the realflag.txt file.

![](https://cdn-images-1.medium.com/max/2000/1*YSJnl67yHwCKSvK9smN_hA.png)

What is this? I thought we had the real flag. Kevin the hacker strikes again. The previous part of each communication had a clue in it that directed us where we needed to go. In the message “*esoteric programming languages*” stands out because who talks like that in reality? Googling the term leads to a Wikipedia article.
[**Esoteric programming language - Wikipedia**
*An esoteric programming language (sometimes shortened to esolang) is a programming language designed to test the…*en.wikipedia.org](https://en.wikipedia.org/wiki/Esoteric_programming_language)

In the list of language names, one stands out - **Brainfuck**. Some clues in the challenge that point to this are: the challenge name itself (Brain_Gamez), the capitalization of *BRAINy* in the challenge narrative, and the capitalization of *BRAIN* and *F*CK* in the hacker’s communications.

![](https://cdn-images-1.medium.com/max/2000/1*mDqNQHECtCDP_olBPQvG4Q.png)

**Brainfuck** ([https://en.wikipedia.org/wiki/Brainfuck](https://en.wikipedia.org/wiki/Brainfuck)) is actually an interesting programming language that uses only 8 commands. Those 8 commands do math operations like increment and decrement pointers. For example, Hello World in **Brainfuck** would appear as the following:

    ++++++++**[**>++++**[**>++>+++>+++>+<<<<-**]**>+>+>->>+**[**<**]**<-**]**>>**.**>---**.**+++++++**..**+++**.**>>**.**<-**.**<**.**+++**.**------**.**--------**.**>>+**.**>++**.**

![](https://cdn-images-1.medium.com/max/2000/1*YI2B3AkGCPcxjyqWXHC4Rw.png)

There’s a variety of online **Brainfuck** compilers we can use to see if Kevin’s message is actually stored in the programming code. We can load the code into an online compiler located at [https://copy.sh/brainfuck/](https://copy.sh/brainfuck/) .

Executing the code listed in Kevin’s message reveals the final flag, HMCTF{Br41n_G4M3z_4r3_Fun} — and the challenge is finally solved.

**Fun Fact:** The real life Kevin M. never worked with a hacker named “Condor”. He actually used the name “Condor” as one of his handles.

