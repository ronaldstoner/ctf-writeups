
## Hackmethod August 2017 — Challenges Write Up

In addition to providing training and security services, the folks over at Hackmethod run a monthly Capture The Flag style challenge for hackers and information security professionals. Make sure you give them a visit at [https://hackmethod.com/](https://hackmethod.com/)

This month’s set of challenges involved something I had no prior knowledge or experience with — 3D Printing. The August set — **Print3d **— included a challenge for an **.STL** file, 3D Printer **G-Code** analysis, and a packet capture that needed to be dissected. I’ve yet to partake in a 3D printer based challenge during a CTF so I was looking forward to tackling the tasks at hand.

![The rules and what to expect](https://cdn-images-1.medium.com/max/2000/1*YpxlH4hBrJivX-5xFY1HkA.png)

### Challenge 1 — Going Out in STL
> # We know that an STL file is a file format native to the stereolithography CAD software created by 3D Systems and used for 3d printers. This file was caught being ex-filtrated out of our network via HTTP headers. We’ve carved the data and reassembled it back to its original form. Help us find the hidden msg.

The first challenge provided a **.STL **file (say that three times fast) that needed to be analyzed. I downloaded and installed a copy of **FreeCAD, **which seemed to be able to view the file type. A variety of other viewers, CAD softwares, and **Paint.NET** were all tried prior to **FreeCAD**, but I’ll save you the time of reading about my failed methods. **FreeCAD **was able to render the image and allow me to navigate the stencil easier than other software. Upon opening the file we immediately see a flag.

![The fake and incorrect flag](https://cdn-images-1.medium.com/max/2284/1*WW3518RLyPvroe97ne1bWg.png)

Submitting the flag above results in a failed message on the **Hackmethod ([**https://hackmethod.com/](https://hackmethod.com/)**) **challenge site. Nope. False flag. We’re not there yet.

After hours of perusing the image, slicing the render into different layers, changing color palettes, attempting different steganography tools, and really over-thinking the challenge, I still didn’t have the correct flag. It was only during one of those hazy 2:00 AM nights (when all hope is lost) that I zoomed in extremely close while doing a fly by of the letters. Something caught my eye.

![What’s that stuff written on the inside of the letter E?](https://cdn-images-1.medium.com/max/2000/1*fVN8GcNx1U28InKK7MIzJA.png)

The letter **E **appeared to have something written on it. What’s is this? A challenge for ants?

![Zooming in and capturing the flag](https://cdn-images-1.medium.com/max/2000/1*wZPathBup0kreGzlYsvIJA.gif)

The flag n0t_hidd3n_3n0ugh was found written in extremely small text. After dissecting the file, splicing the image a million different ways, and other witchcraft and voodoo incantations, it was just extremely small text that needed to be found. On to the next challenge.

### Challenge 2 — gcode.txt
> # We’ve intercepted a file called gcode.txt and believe there is a secret msg hidden in the text file. We are not sure what it is or even how to read it.

I’m not familiar with **G-code** ([https://en.wikipedia.org/wiki/G-code](https://en.wikipedia.org/wiki/G-code)) and this challenge took a lot of research and time on my part to really understand what I was looking at. **G-code** is basically a language detailing instructions like co-ordinates to computerized machine tools (laser cutters, some 3D printers, etc), laser/printer power levels, and other important information when cutting or printing an image.

A Rich Text format (**.rtf**) file containing ***11,685*** lines was provided with G-code instructions that seem to represent a 3D printed image.

![I always wait for the temperature to be reached](https://cdn-images-1.medium.com/max/2000/1*u7t03EzcVuhOBl92-XolPw.png)

Scrolling through the file was some meta-data for the **.rtf **text properties as well as some output from the program that generated the file — **Slic3r**, but nothing seemed out of place upon first glance. Let’s load up the **G-Code** in a tool and see what the image actually is.

![](https://cdn-images-1.medium.com/max/2000/1*YQxPuCqYVgtPEUfAAW5I6g.png)

It’s the **Hackmethod **logo printed in two pieces. I wanted to make sure there was no messages being written and hidden in the stencil, so I simulated through each line of **G-Code** to see what the 3D printer “arm” was actually doing and where it was actually moving. After parsing through all 11,000+ instructions I didn’t see anything out of the ordinary. Fail.

![I must have watched this simulation a hundred times](https://cdn-images-1.medium.com/max/2000/1*Vwn-feROo2T_Md0F2jDR2g.gif)

Since I had no prior knowledge of **G-code, **I ended up doing some research to determine what it was actually representing. Each letter in the code represented a different function of the 3d printer. This would prove to be useful later on.

![What the symbols in G-code represent](https://cdn-images-1.medium.com/max/2000/1*QX15MSxJqvGIx0C4pr-iaQ.png)

At this point no one had solved the challenge yet, and eventually a hint was released in the form of a riddle.
>  What do fresh fish, forest fires and the alphabet all have in common?

Answer: The letter F. Taking the knowledge of how **G-code **works, along with the riddle above, led me to believe there might be something hidden in the F portion of the codes. F codes wouldn’t be affected by simulations or someone changing that value as it is used for defining *Feed Rate*, which isn’t needed unless one was actually 3D printing the object. This seems like a good place to hide data.

Originally I was iterating through a bunch of text manipulation commands such as **sed **and **awk **to identify and cut out unique F code values, but I should be able to achieve the same result with a one liner (thanks for the idea slickm0nty!).

grep -Eoh 'F[01]\d{2}\.' gcode.txt | sed 's/F//' | tr -d '.',

![One liners for the win](https://cdn-images-1.medium.com/max/2272/1*T30bSuCxrsjeiwLBBOUamw.png)

With everything else in the file stripped away, and the list of unique codes now in hand, we can convert the numbers to their ASCII character equivalent. Because we’re hackers let’s take it one step further and actually do the ASCII conversion on the command line. Taking the previous one liner, and using **awk** to perform the ASCII conversion, we get the following:

grep -Eoh 'F[01]\d{2}\.' gcode.txt | sed 's/F//' | tr -d '.', | awk '{printf "%c", $1}; echo

![Nice, neat, and correct output](https://cdn-images-1.medium.com/max/2272/1*l_EZqfWKCqMdGFLxO1xygw.png)

The flag L1V3_0r_d13_by_7h3_gcod3 is revealed and the challenge has been solved.

### Challenge 3 — The Key is in the SNMP
> # Synfidel was hired as a Security Consultant by a Big Box office supply company. During his consultation he was able to perform a man-in-the-middle attack. Can you help him find the secret flag office workers were passing around?

This challenge involved combing through a network packet capture of **SNMP **traffic, which is a protocol that provides network and diagnostic information to system administrators. Due to the challenge name I figured the flag was encapsulated in one of the packets in the capture.

![Viewing the SNMP packet capture in Wireshark](https://cdn-images-1.medium.com/max/3484/1*Ioc45QeEWdm7qwdkM2DkSQ.png)

The capture contained usual looking **SNMP **traffic including system diagnostics, community names such as **public, serenity!**, **firefly**, a TON of false flags and narratives, and what looked like some failed connections and file transfers. These connections and file transfers stood out to me so I decided to focus on those packets specifically.

![Finding the failed connections](https://cdn-images-1.medium.com/max/3428/1*AvMwAnurM-ocVuFvT1exLg.png)

In ICMP packet **5297 **shown above it seems there was a failed connection due ***Destination Unreachable***. Looking inside a hex dump of the packet shows an attempted transmission of a **GPG** encrypted file with the name of **firefly**. I wanted to look at that **GPG **file and see if it contained the flag.

![](https://cdn-images-1.medium.com/max/3436/1*PPNY747qrQY73o9ej_AUMA.png)

TCP Packet **5303 **then followed shortly in the stream after the failed connection, and it had a larger size than most of the other packets. I selected the **Data **line on the identified packet and from the **Wireshark **main menu bar selected ***File ***and then ***Export Selected Packet Bytes***.

![](https://cdn-images-1.medium.com/max/2284/1*uLp9s3NyprVpO1rwGmj7Sw.png)

Running the **file **command on the saved output shows that we’ve retrieved the **GPG **encrypted file that was listed in packet **5297 **prior. I attempted to decrypt the file on the command line and was immediately prompted with a password. Since I already enumerated through the packet capture, and identified some unique values such as the **SNMP **community names, I attempted to try those as a password. The **SNMP **community name serenity! worked and I was able to see the decrypted plain text output of the file.

![Decrypting the file carved from the packet capture](https://cdn-images-1.medium.com/max/2220/1*vL21v6kvreh17f-IPhpnew.png)

The file itself was a **Cisco IOS **configuration used for **Cisco **routers and switches. I knew that encrypted credentials and secrets could be cracked in some **Cisco **configurations, but this did not seem to be the case. Looking further in the file showed me a **base64 **encoded community string that looked suspicious.

![Looking further into the Cisco IOS configuration file](https://cdn-images-1.medium.com/max/2272/1*C05D3iwV-QWOs9BqKG0BYA.png)

The **base64 **encoded string ***SE0tQ2hhbGx7U1lOX0ZsQGctZmlkZWxfQUNLLUZJTn0K ***was decoded and the output was displayed. The key was in the SNMP.

![Decoding base64 and capturing the flag](https://cdn-images-1.medium.com/max/2276/1*DXDNWVFFuDgzoz7pZ_mD5g.png)

The flag SYN_Fl@g-fidel_ACK-FIN was captured and the challenges for the month of August were now completed. I was able to get the first solutions for the **STL **and **G-code** challenges which granted me those sweet extra first blood bonus points.

