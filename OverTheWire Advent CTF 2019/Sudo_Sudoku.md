
## OverTheWire Advent Bonanza 2019 — Sudo Sudoku

![](https://cdn-images-1.medium.com/max/3840/1*_vpzO6M-uTikvYBxVk45Dg.jpeg)

This write up is part of a bigger Capture The Flag competition. You can see other challenge write ups on the main post [here](https://medium.com/@forwardsecrecy/overthewire-advent-bonanza-2019-capture-the-flag-competition-66c50671c641).

### The Challenge

*Santa’s little helpers are notoriously good at solving Sudoku puzzles.
Because regular Sudoku puzzles are too trivial, they have invented a variant.*

![The Sudoko puzzle to solve](https://cdn-images-1.medium.com/max/2000/1*AMvY4RW3qe_HTTloqktdaQ.png)

*In addition to the standard Sudoku puzzle above,
the following equations must also hold:*

*B9 + B8 + C1 + H4 + H4 = 23
A5 + D7 + I5 + G8 + B3 + A5 = 19
I2 + I3 + F2 + E9 = 15
I7 + H8 + C2 + D9 = 26
I6 + A5 + I3 + B8 + C3 = 20
I7 + D9 + B6 + A8 + A3 + C4 = 27
C7 + H9 + I7 + B2 + H8 + G3 = 31
D3 + I8 + A4 + I6 = 27
F5 + B8 + F8 + I7 + F1 = 33
A2 + A8 + D7 + E4 = 21
C1 + I4 + C2 + I1 + A4 = 20
F8 + C1 + F6 + D3 + B6 = 25*

*If you then read the numbers clockwise starting from A1 to A9, to I9, to I1 and
back to A1, you end up with a number with 32 digits. Enclose that in AOTW{…}
to get the flag.*

### The Solution

This is a tough challenge consisting of math and programming in order to find the flag. One must solve a Sudoku puzzle by finding 32 digits, but the puzzle must also meet a list of very specific conditions. Due to this, only an extremely small subset of Sudoku solutions (in this case, one) will meet the conditions and unlock the final flag.

Enter **go** programming guru and CTF team member **solipsis**.

In order to solve a challenge such as this, one must do some paper math and preparation work in order to prune and reduce the search space early on. Since we have a fixed number of already filled out numbers, ranges for the missing inputs could be figured out — which helps reduce the complexity and brute force space search by a few orders of magnitude.

Programming this script to “fail early” as soon as it finds any value that exceeds those in the list of conditions, rather than checking conditions once it has an entire puzzle solution, also helps to speed things up quite a bit.

These techniques, combined with a descending 9 ->1 number order, help to trigger the failure conditions faster, and reduces magnitudes even further.

 <iframe src="https://medium.com/media/b6589a4741fd55d97a0040647ed6abf3" frameborder=0></iframe>

After running the script for some time, a final Sudoku solution that meets the list of requirements is found.

![Success!](https://cdn-images-1.medium.com/max/2000/1*i-XSplUDmAfuvpwWfqyAyg.png)

Per the challenge description, the 32 found numbers are compiled into a single string for a final flag of **AOTW{86472953189247356794813521457639}**.

