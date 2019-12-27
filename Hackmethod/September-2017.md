
## Hackmethod September 2017 — Challenges Write Up

September saw the last of the 3rd quarter challenges from the team over at Hackmethod ([https://ctf.hackmethod.com/challenges](https://ctf.hackmethod.com/challenges)). Starting in October the team will be moving over to a brand new more efficient CTF site and will be making some rule changes. This monthly set of challenges included a buffer overflow that needed to be exploited, and two new network packet captures of varying difficulty.

**Challenge 1 —TurBoFans at Max!**
> # There is a service hosted on the challenge site on port 1337. Defeat this service to get the flag. Below is the compiled binary to reverse on your own. Strings may not yield you much info. =D
> # To connect: nc challenge.hackmethod.com 1337

Based off the name and description of this challenge it seemed like we were venturing into buffer overflow territory. Ladies and gentlemen, buckle up your seat belts. If you are seated next to an emergency exit, please read carefully the special instructions card located by your seat. If you do not wish to perform the functions described in the event of an emergency, please ask an attendant to re-seat you.

Luckily the overflow itself didn’t include protections such as **Data Execution Prevention (DEP) **and **Address** S**pace** L**ayout** R**andomization** (**ASLR) **and it could be exploited as long as we controlled the EIP register. The challenge came with a local executable that could be used for testing in the form of a 32-bit LSB executable named **TryMe**. The executable takes input from a user and echos it back on the terminal. Since this was storing and echoing a text string, it probably didn’t have the proper bounds set and could possibly be overflowed.

I loaded the executable in my copy of Binary Ninja ([https://binary.ninja/](https://binary.ninja/)) and was able to see immediately the function that returned the flag. The executable was calling the function **datFlagTho **which printed a congratulations message and printed the contents of flag.txt. If EIP could be pointed to **0x80485e0 **then the function would execute thereby exposing the flag.

![](https://cdn-images-1.medium.com/max/2000/1*awFFFqm1-s1TKiVFEkjKSg.png)

![Disassembling the code to see what’s going on — notice the ‘cat flag.txt’?](https://cdn-images-1.medium.com/max/2272/1*Yv_9D7I3X6B2Io9Nnjufcw.png)

Using a fuzzing script I was able to find out that by sending a string of 260 A’s to the program it would crash and cause a segmentation fault. I was able to test this in the linux **gdb** debugger. Once I verified the value of 260 was correct, I added 4 B’s to the string buffer and some additional C’s as padding. In the screenshot below, you can see that the string overwrites EIP with the 4 B’s (represented as **0x42424242**), which means that the program could now be controlled. Replacing the EIP with any value we choose would cause the program to jump to that value, or effectively wherever we wanted to in the code.

![Testing for the seg fault and overwriting EIP](https://cdn-images-1.medium.com/max/2000/1*vjANpPxP7FHC--ZKBns0ow.png)

A perfect candidate to jump the program to would be the function that wished us congratulations and printed the flag (remember **0x80485e0**?) A python script that I was using for the initial fuzzing of the executable was updated to include the new return address (in **little endian** format) as the value to overwrite EIP.

![Buffer overflow script in Python](https://cdn-images-1.medium.com/max/2896/1*BvSWMP5dfJrXdoy8Hyky3g.png)

The overflow script was tested locally against the **TryMe** executable and the console showed the congratulations message with a file read error since **flag.txt** didn’t exist on my testing server. It was time to take the script and run it against the challenge site. Executing the script revealed the flag and awarded points for the challenge completion. Unfortunately I lost the final output screenshots from a data crash, but my proof of concept executed and the challenge site returned the flag.

Since we knew all the characters needed for the overflow, and because we’re l33t hax0rs and like to optimize things, we can actually do a one liner buffer overflow by creating the string buffer on the command line and piping it into **netcat**.

    python -c ‘print “A” * 260 + “\xcb\x84\x04\x08”’ | nc challenge.hackmethod.com 1337

Running that one liner will buffer overflow a remote service. A remote buffer overflow in only 83 characters. Neat!

### Challenge 2— Pcap: “EASY”
> # Synfidel returns! If you recall, last time he did a consultation for a Big Box office supply company. This time his journey’s take us to a Mom & Pop’s Pi shop. During his ‘pro bono’ consultation (bc he is a standup guy) he discovers that Mom & Pop are not securely logging into their Pi server. Let’s see if you find the same unfortunate details Synfidel did.

This month we get 2 more network packet capture files to analyze. The capture file itself was loaded into **Wireshark** so I could analyze the traffic better. The data looked like a normal user session including some web forum browsing activity and normal protocol chatter. Through out the capture some **telnet** data was interspersed so I wanted to look at that more in depth.

![Analyzing the network packet capture and identifying the telnet protocol](https://cdn-images-1.medium.com/max/3780/1*huMYqFbj_XP2j3uc3_W5hg.png)

The **telnet** protocol does not use encryption and as a result broadcasts it’s traffic in clear text — so if I could read the packet data I could see what the user typed during the session. Taking a look at a random telnet packet showed that it contained the letter “h” in it’s data field. Since this looked like a single keystroke, I wanted to compile all data so I could get a clearer picture of what was going on. I selected the data field and did a follow of the TCP stream in **Wireshark, **which consolidates all the data and shows what was typed.

![The telnet packet](https://cdn-images-1.medium.com/max/2912/1*8fYM3tJcj6n0OY9yxM1TJQ.png)

![Following the TCP stream and getting a consolidated view of what transpired in the telnet session](https://cdn-images-1.medium.com/max/2400/1*7tebLcLjXf34HiCRs4cz1Q.png)

The TCP stream showed that the user typed in a username and credentials during the **telnet** session and revealed their contents. I could see the the flag I was looking for in the Password field. WhoUsesTelnetAnymore? was submitted as the answer and the challenge was solved.

### Challenge 3— Pcap: WTF!? Part II
> # As if our first USB pcap wasn’t easy enough to solve, here is one that might change things up a bit. Find and decrypt the secret key.

The 2nd of the packet capture files contained USB device data, similar to the July challenge. I decided to approach this challenge the same way.

![The USB packet capture](https://cdn-images-1.medium.com/max/2760/1*ZKH5Z-XM6_t4rtWgn8hOew.png)

The above packet capture shows there is **Leftover Capture Data** in the **telnet** packet with values being stored in the 3rd bit. Using the **tshark** command (see July’s write up for syntax) the 3rd bit in the capture data can be extracted for further analysis.

![Extracted Data from the Leftover Capture Field](https://cdn-images-1.medium.com/max/2000/1*FeXQ7nOePPmdCJfTppFvHw.png)

I remembered from July’s challenges that in USB challenges these values can sometimes represent keystrokes and/or mouse movement coordinates. I used a script from a fellow CTF solver to convert the values to USB key strokes (unfortunately the script cannot be shared here). Converting the values displays the following output:

![Decoding the text that was typed](https://cdn-images-1.medium.com/max/2000/1*rN31eyk51hSIrwsnrFiX5A.png)

The text appears to be someone typing and hitting the ENTER key. At the very end is a string that is not displayed in clear text and appears to be the next part of the challenge.

This part took me some time as I tried a variety of ciphers and decoding techniques on the string to no avail. After thinking about the challenge for a few days, I had an idea based of the type of challenge it was. Since the packet capture and the data was USB keyboard based, I figured the string in the output was being typed correctly. The only way to type that data would be to do it with those specific characters on purpose, or to use a different key mapping.

I tried converting the string from the **QWERTY **keyboard layout to **DVORAK **([https://en.wikipedia.org/wiki/Dvorak_Simplified_Keyboard](https://en.wikipedia.org/wiki/Dvorak_Simplified_Keyboard)) and see if anything appeared. For those of you unfamiliar with **DVORAK **it’s essentially an alternate way to arrange the keys on a keyboard in the name of efficiency.

![Converting the string from QWERTY to DVORAK](https://cdn-images-1.medium.com/max/2000/1*J3dgjU_g2eGKn5mnT6H3zQ.png)

The flag 3at_i7_Qw3rty was decoded from the string and the challenge was now solved.

### Final Word

For the 3rd quarter the challenges ran by the HackMethod team changed from a monthly contest to a quarterly contest. In order to win the contest hackers had to complete challenges from three consecutive months and battle for additional first blood points. I’m happy to report that for the 3rd quarter I was able to place 1st. As a result, I received some books and stickers from Hackmethod and had a ton of fun while learning some new skills.

For the month of October I’ll be solving more challenges, but I’m also spending my time now creating challenges for other hackers and professionals to solve. In the month of October, Hackmethod will be hosting a forensics challenge I created called Brain_Gamez. Be sure to head over to the new Hackmethod CTF contest site at [https://ctf.hackmethod.com/](https://ctf.hackmethod.com/), solve my challenge, and join in on the fun.

![3rd quarter scoreboard](https://cdn-images-1.medium.com/max/2700/1*iZdoxUOKV256u1zyGLf7ow.png)

