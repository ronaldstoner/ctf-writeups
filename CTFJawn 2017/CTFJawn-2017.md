## CTFJawn 2017 @ BSides Philly Write Up

Security BSides recently came to Philadelphia, and with it a host of fun hacker related activities. One of those was a brand new first time Capture The Flag challenge — **CTFJawn2017**.

**CTFJawn 2017** (aptly named for a Philadelphia conference) included 4 challenges which revealed flags that needed to be transformed and combined into one big final flag. No instructions were given on how to submit this final flag, and it was up to the participant to piece together the clues and information provided. The **Hackmethod **(www.hackmethod.com) team and I decided to take up this challenge. I’m happy to report that after all the hours spent, numerous Slack messages, and sweat and tears spent the dust had settled and we were able to achieve the first solve for the solution.

### **Challenge — 0x1**

![This was the only thing listed for Challenge 0x1](https://cdn-images-1.medium.com/max/2000/1*oiYhw7_KNhqLp6g8prc_lA.jpeg)

Challenge 0x1 was simply a picture of Good Ol’ Ben Franklin. For those of you who don’t know, Ben is one of the Founding Fathers and a HUGE icon in the city of Philadelphia. Relying on my forensics experience I started checking the usual avenues and tools like **steghide**, hex editing the file, and checking the **EXIF** data. Inside the **EXIF** data (metadata stored regarding settings for photos) showed that there was a URL in one of the fields.

[http://ctfjawn.org:8000/6368616c6c656e6765307831.htm](http://ctfjawn.org:8000/6368616c6c656e6765307831.htm)

![Good advice, but it’s not the flag](https://cdn-images-1.medium.com/max/2000/1*kqhNXJdD6JCqaPVoALOPqg.png)

Navigating to the URL showed some text, but it was not the flag we were looking for. Knowing that there was other ways to query that website and have data returned, we started playing with **cURL** and it’s various options. Doing a **GET HEAD** request through cURL revealed some information, which included the flag.

![Running curl -I and getting the flag!](https://cdn-images-1.medium.com/max/2000/1*5VcVDp-1MB1Y5MHRI1-Nfw.png)

### Challenge — 0x2

Challenge 0x2 revolved around a packet capture (.pcap) file that needed to be analyzed. Loading up the packet capture in **Wireshark **showed a variety of HTTP/HTTPs traffic, including traffic over the **TOR** network. **TOR **allows anonymous browsing capabilities using onion routing technology. Instead of websites ending in .com and .net, **TOR **websites end in .onion. A few of the **DNS **lookups in the packet capture show traffic attempting to go to a few onion sites on the **TOR **network.

![Finding the TOR .onion domain names](https://cdn-images-1.medium.com/max/2000/1*zGuOljN4mesX7AAjqtNdnQ.png)

Browsing to these domain names using the **TOR Browser Bundle **found a webpage hosted on **6uu4urm652npg23t.onion.**

![Finding the TOR website](https://cdn-images-1.medium.com/max/2000/1*Zg0bhlomrprS6P0jWVGG7A.png)

On the website were a few clues — a **base64** encoding string, a link to a reversed hex string, and a number that was labeled **_botjawn**. Decoding most of these strings (which were prevalent throughout the challenges) resulted in various easter eggs, Rick Rolls, and a music video of The Dead Milkmen — a popular Philadelphia punk band.

 <iframe src="https://medium.com/media/98f4f4690b2826dbab796d1c17c6c086" frameborder=0></iframe>

After burning through the false flags and various easter eggs in the challenge, we took a look at the source code for the website. While the code itself didn’t show much, there seemed to be a bunch of white space and returns at the bottom.

![That doesn’t look like source code!](https://cdn-images-1.medium.com/max/2000/1*sTRaMf_kMSpx7fd_U-jpJA.png)

Commented out at the very bottom of the source code is the flag. On to the next challenge.

### Challenge — 0x3

Challenge 3 related to a spreadsheet hosted on **Google Docs**, and some comments posted in a **Gist**.

Gist: https://gist.github.com/richrichard/d6eb532db2899e5d36211d4b15d2e2fd#file-pd

The spreadsheet contained some values related to “BitJawn” mining and while the spreadsheet itself seemed legitimate, the data contained did not.

![The spreadsheet, possibly containing hidden data](https://cdn-images-1.medium.com/max/2000/1*pKRsg4lGmLGKuhKmBTIdfQ.png)

From the comments listed in the **Gist**, we know there’s a few peculiar things about this document. We see a few numbers listed and a note about how the spreadsheet is centered on column 215. I thought this was interesting for a few reasons because 215 is a Philadelphia Area Code, but it also shows that there’s columns hidden prior to where the spreadsheet data starts. The columns hidden between Columns A and HE (Column 213) were unhidden, but no new data was found.

After looking at the spreadsheet further it became apparent that the numbers under the columns corresponded to the column name (in this case 213 = HE). Since we see the number 213 in cell 4 of Column HG, we can assume the numbers listed in the spreadsheet relate to the names of columns.

At this point the team decided to generate all column names, but when dealing with numbers like 53,082,687,204 (Cell 8 in Column HG) our code ran out of memory due to the large amount of entries that needed to be generated. A more optimized python script was found and used to generate a corresponding column name.

Script: https://gist.github.com/ronaldstoner/aaf65ec03a9b27cd74c186c1f9bba4de#file-excel-py

We took each number from the spreadsheet and passed it through the script. This generated all the column names and revealed a message.

**“WE ASKED FOR MOJO NIXON THEY SAID HE DONT WORK HERE WE SAID IF YOU AINT GOT MOJO NIXON THEN FLBK THREE IS FOUR SIX EIGHT FIVE FOURTEEN FOUR TWELVE SIX TWELVE FOUR THREE FOUR TWO FOUR ZERO SEVEN THREE SIX NINE SEVEN TWO FOUR SIX SEVEN TEN FIVE NINE SIX TWO FOUR FIVE SEVEN”**

In what started to look like another false lead (more Dead Milkmen) we see that Flag 3 is eventually revealed. In the above example the team believes the word “FLBK” to be “FLAG”. I’m not sure if this was an issue with our script, or a typo by the challenge creators. Converting the rest of the numbers in the above text to hex gives us the following string, and the flag for Challenge 0x3.

**“FLAG THREE IS 4685e4c6c434240736972467a5962457”**

### Challenge — 0x4

*Host 13.58.63.15 is waiting for a UDP packet with a key as the data string, port 2017*

Three separate packet capture files relating to 802.11 WiFi traffic were handed to the team to be analyzed. Since all three of the packet capture files were encrypted, we needed to first break the WiFi encryption from the packet capture so we could see what traffic was being sent. Yes kids, you can break WiFi keys using packet captures thousands of miles away from the actual Access Point/Router.

![A bunch of encrypted WiFi packets](https://cdn-images-1.medium.com/max/2000/1*TnchJGUaK1W7bFb-or0fbQ.png)

Generally these types of attacks use a brute force or dictionary attack method, but what word list would we use? Previously in the CTF we saw an easter egg pointing to a Ben Franklin/Philly themed word list hosted on **Github**.

[https://raw.githubusercontent.com/TheRichardSaunders/loot/master/lists/aphorism-list-4.txt](https://raw.githubusercontent.com/TheRichardSaunders/loot/master/lists/aphorism-list-4.txt)

![These look like GREAT WiFi passwords!](https://cdn-images-1.medium.com/max/2000/1*DoHD3rqTASKxvkMKIuvIRA.png)

The list happened to be Aphorism sayings written by Ben Franklin in **Poor Richards’ Almanac**. The WiFi cracking tool **aircrack-ng** was loaded up against one of the packet capture files, and the WiFi key was hacked.

![Hacking the WiFi password in seconds](https://cdn-images-1.medium.com/max/2000/1*uGkFx-0C-G-WP1WzMCMMvQ.png)

The WiFi key “**Bydiligenceandpatiencethemousebitintwothecable**” was captured and then applied to the rest of the packet capture files. This decrypted the WiFi traffic and allowed us to see what was actually going on. The challenge description was “*Host 13.58.63.15 is waiting for a UDP packet with a key as the data string*” so we took the newly cracked key and sent it.

Using the **Linux **command line and **netcat, **we sent the UDP packet with the correct key to the IP listed in the challenge.

![Sending the UDP packet, and getting the flag!](https://cdn-images-1.medium.com/max/2000/1*YvzQEjeGcsyp16SDaG5Exw.png)

### Final Flag

Now that we had all 4 flags, we needed to figure out what to do with them and how to submit. On the challenge **GitHub **repository we find a file that gives us a few clues as to what needs to be done.

![Instructions listed at [https://github.com/TheRichardSaunders/loot/blob/master/scripts/sgalf.py](https://github.com/TheRichardSaunders/loot/blob/master/scripts/sgalf.py)](https://cdn-images-1.medium.com/max/2000/1*PnicHcuCYgx70QZjCA1atQ.png)

So the flags needed to be **concatenated**, **hexed** (they already were), **reversed**, **base64 encoding/decoded**, and then reversed again per the instructions. **Python **was able to step in, and we were able to code a quick script to do the conversions.

Final script: https://gist.github.com/ronaldstoner/d159febdb67d8bf2b3fb7d47e2102ab8#file-decrypt-py

Running the script gave us the output we needed — **“Most of the learning in use, is of no great use.”**

![Decrypting the final flag](https://cdn-images-1.medium.com/max/2000/1*Iiu9Os25xpDgQ_oLKTY0ow.png)

Now that we had the final flag, what did we need to do with it? Way back in Challenge 0x2 (and it’s screenshot in this writeup) we see that the TOR website makes mention of a **“free node”** and **#ctfjawn**. Visting the **FreeNode** IRC server and **#ctfjawn** room showed that there was a bot listening by the name of **_botjawn**. Challenge 0x2 had a number listed next to the term **_botjawn**. Let’s send that number from Challenge 0x2’s TOR webpage to the bot and see what happens.

![A _botjawn easter egg! Don’t worry, it’s just The Dead Milkmen again](https://cdn-images-1.medium.com/max/2000/1*avLImkpkffT9__u3VlkgHQ.png)

Sending some text to **_botjawn **shows that it repeats the text unless one sends a string the bot is specifically looking for. We took the final flag **“Most of the learning in use, is of no great use.” **and sent it to the bot.

![Whoa! Almost there!](https://cdn-images-1.medium.com/max/2000/1*lVP69tQX6u8yxvhN0gX-KQ.png)

The bot tells us to look for a **ctfjawn** binary and give it the string “**Well done is better than well said**.” The ctfjawn binary located on the challenge **Github **is executed ****and the requested input is passed to it.

![Running the binary and passing it the required string](https://cdn-images-1.medium.com/max/2000/1*zcVqbuF_1kog59DTh8tbiQ.png)

We receive the coordinates Lat/Long: MzkuOTQ5NTMyOSwgLTc1LjE0NjYwNjE=

The above string is converted from **base64 **and we get a latitude and longitude of 39.9495329, -75.1466061, which is located in Philadelphia.

The coordinates are submitted to **_botjawn **and the challenge is now completed and solved.

![For The Win!](https://cdn-images-1.medium.com/max/2000/1*6Gr4yedYEODbPeXthQRC8Q.png)

### Thanks

Thank you to the **CTFJawn 2017** team for putting on this Capture The Flag competition. While some things worked well and others did not, I feel this is a successful first competition and I know myself and the team had a lot of fun.

Additionally thank you to the **Hackmethod** team for powering through the challenges together. If you’re not aware of **Hackmethod**, check them out at [**www.hackmethod.com](http://www.hackmethod.com)**. Special thanks goes out to **hellor00t**, **incidrthreat, daddycocoaman**, and **xaeroborg**. You dudes saw it through and toughed it out until the end.

