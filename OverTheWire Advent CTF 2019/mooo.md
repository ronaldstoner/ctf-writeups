
## OverTheWire Advent Bonanza 2019–mooo

![](https://cdn-images-1.medium.com/max/2000/1*EkHtlgVdgKnbe4kqZm38bw.png)

This write up is part of a bigger Capture The Flag competition. You can see other challenge write ups on the main post [here](https://medium.com/@forwardsecrecy/overthewire-advent-bonanza-2019-capture-the-flag-competition-66c50671c641).

### The Challenge
>  ‘Moo may represent an idea, but only the cow knows.’ — Mason Cooley

Mooo was one of the more fun challenges and provided us with a web service running on a specific port and IP address. Navigating to the site brings us to an implementation of **cowsay**. **Cowsay** takes input from a user and displays it in an ASCII art formatted cow.

![The cowsay program (banner at bottom is cut off)](https://cdn-images-1.medium.com/max/2000/1*iCT8MVFrR0GjQ98o_yZPQQ.png)

We know the name of the program due to the banner at the very bottom of the page (not shown here) listing the program version as **Powered by [cowsay 3.03+dfsg2–4](https://packages.ubuntu.com/bionic/cowsay). **As a hacker, if we can get access to the source code then we can start looking at places to poke and prod. **Cowsay **happens to have it’s source code listed at [https://github.com/schacon/cowsay/blob/master/cowsay](https://github.com/schacon/cowsay/blob/master/cowsay).

![Part of the cowsay source code](https://cdn-images-1.medium.com/max/2000/1*vGYStlxNQxvZORCysrpNng.png)

### The Solution

After reviewing the Github and source code, we know that **cowsay** is written in the **Perl** programming language. Unfortunately, the only input field we’ve seen so far places text in the cow’s speech bubble. Attacking this did not seem to yield any results, as it seems the input field is being sanitized. We need to look elsewhere for an attack vector. Fortunately, a “custom” cow template exists that gives us more input fields to play with.

![The custom cow template](https://cdn-images-1.medium.com/max/2000/1*x_-FSRNDRn0MAYvabfwJBQ.png)

We now know that the program is written in Perl, and we have more input fields to play with. No attacks were found after trying some web application and string escapes in the **Message**, **Eyes**, and **Tongue** field, but something interesting was found when testing things against the **Cow** text field.

**Perl** is not one of my strongest or favorite programming languages. Someone on my team decided to **RTFM** and found a gem inside the Perl documentation located at [https://perldoc.perl.org/perlop.html](https://perldoc.perl.org/perlop.html).

![The key to the kingdom, and a great Perl escape](https://cdn-images-1.medium.com/max/2000/1*05BWHPgZVyOJBwHzs9AOSg.png)

They noticed that some Perl scripts contained **EOF **(End of File) or **EOC** (I’m assuming End of Command), indicating that the Perl session was to exit after finishing it’s code processing. We tried to pass **EOC, **which seemed to work without reporting any errors. After that, we tried chaining commands with the linux **id** command to see if we had escaped Perl and reached a shell.

![Cowsay failed, but have we?](https://cdn-images-1.medium.com/max/2000/1*ScxeYbnRqaSuGY2vh388EA.png)

The server didn’t like our **id **command, so it didn’t seem we were at a shell yet. We did get an error message when adding the **id** command, whereas we did not when previously trying just **EOC**, so it seems we’ve escaped Perl, but now we’re….somewhere else entirely.

![No, not there.](https://cdn-images-1.medium.com/max/2000/1*aQApTBsEdy7eEbqtjhMirw.jpeg)

Knowing that web applications can only be hosted by a variety of services, we try a variety of commands in different syntax. When attempting a **python** module import, we no longer receive any errors.

![No errors!](https://cdn-images-1.medium.com/max/2000/1*PhZRjM2aHpsYkNbhElEjgA.png)

Did we escape from **Perl** and land in **Python**? I think we did! Let’s see if we can get **python** to execute shell commands for us using the **os.system** call.

![The final flag](https://cdn-images-1.medium.com/max/2000/1*a6KKQOw1sDH0eLeJV9HUSA.png)

The **python** call executes and we get the final flag. Mooo!

