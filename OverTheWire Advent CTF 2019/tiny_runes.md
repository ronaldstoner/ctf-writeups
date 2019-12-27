
## OverTheWire Advent Bonanza 2019–tiny runes

![](https://cdn-images-1.medium.com/max/2000/1*SOEl9Z3Sy_1j6jPtXi_5zA.jpeg)

This write up is part of a bigger Capture The Flag competition. You can see other challenge write ups on the main post [here](https://medium.com/@forwardsecrecy/overthewire-advent-bonanza-2019-capture-the-flag-competition-66c50671c641).

### The Challenge

*One of Santa’s Little Helpers received an unusual Christmas wish, a copy of the yet to be released Deus Hex game. All they managed to find were fragments from the dialogue system. Can you decode the last one?*

### The Data

The “tiny runes” challenge was a reverse engineering and forensics challenge that included an archive containing 4 binary files containing speech text data for a game engine. Files 1 through 3 included a .txt file showing the game text, so that competitors would have examples to reference. The goal was to take the binary data for the fourth file, and come up with the corresponding text (hopefully containing the flag).

![An example of provided game engine text](https://cdn-images-1.medium.com/max/4200/1*A8r4cV_KnN0julmYGq35HQ.png)

![Contents of the binary file](https://cdn-images-1.medium.com/max/2000/1*VnQzxHTZTGrAdnkmEfUuug.png)

While we didn’t see anything in the binary files using things like **strings**, the real magic was looking at the hex data (per the game name in the clue) of the file in order to see what bytes were being read.

![A hex dump of the example binary file #1](https://cdn-images-1.medium.com/max/2000/1*tPig2d97ZPS7r49Qiz5WNw.png)

All 4 provided binary files had mostly the same bytes and format, up until the section starting at **0x329**. Since the data was different between all 4 files, it was determined that this was where the speech text data was being stored.

Each file had the values **00 00 00 XX **in **0x329** to **0x3BF**, with the last byte seeming to indicate the size of the text about to follow.

We know from the 1st binary file and text that the line starts off with “JC Denton”, but the next bytes we are looking at currently show **05 01. **How does this map to the letter “J”?

![Binwalking for more](https://cdn-images-1.medium.com/max/2000/1*wFr83lIP9tRon-YM36X_hQ.png)

Running the **binwalk** or **foremost** forensic tools on the binary files not only shows text data, but we see a **PNG** image file that we can carve out and extract. The image file itself appears to be a character legend, showing a different arrangement of game text characters.

![The extracted PNG file.](https://cdn-images-1.medium.com/max/2000/1*cnbz6W9FLoLgHnKmG7qW0w.png)

### The Solution

Remember we were trying to figure out how to get the next bytes in the binary file, **05** and **01**, to represent the character “J”? From the above legend, if we count starting from 0 from the top down, left to right — “J” is **01** row down, and **05 **characters over. Remember to start counting from 0. “C” would be **03** across, and **0A** rows down. It seems the text is being encoded in the file using a grid pattern with coordinates to which character is which.

Using this, we can reverse engineer the 3 provided binary and text files to generate our own character mapping so that we can process the 4th binary file, and get the final text.

![Checking the bytes for the 4th binary](https://cdn-images-1.medium.com/max/2000/1*bK3gPZJ-HmeCT_nGm8uBSQ.png)

The bytes from 0x399 to 0x3D8 contain our flag string.
>  07 04 04 05 04 0A 04 09 01 09 00 06 00 09 05 03 00 0A 02 02 05 03 02 02 07 03 02 00 00 0A 00 0A 01 05 00 05 02 02 06 02 01 04 03 08 01 05 02 02 06 02 02 00 07 03 04 02 05 03 00 0A 07 09 06 07

Using the legend above, we can start working through the byte pairs/coordinates by hand, or use an automated script. **07** across and **04 **down would be the character “A”. **04** across and **05** down would be the character “O”, and so on and so on until we get the final flag — **AOTW{wh4t_4_r0tt3n_fi13_f0rm4t}**.

