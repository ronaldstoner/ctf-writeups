
## Google CTF 2017: Mindreader
>  Your Princess Is In Another Castle

The Google 2017 Capture The Flag qualifiers have come and gone. I was lucky enough to be a part of the Hackmethod ([https://hackmethod.com](https://hackmethod.com/)) team this year. If you don’t know what a digital version of Capture The Flag entails, it’s usually challenges where a person or a team of people have to hack into systems, defeat cryptography, reverse engineer executable programs, participate in attack and defend war games, or any combination of the above. While the challenges themselves were EXTREMELY hard this year, I was able to contribute to the team and solve one of the “Miscellaneous” challenges.

![The Google CTF 2017 Challenge Board](https://cdn-images-1.medium.com/max/2000/1*2-3EDtgwtQlly7QdWEfNOg.png)

## **Mindreader**

The goal of this write up is to explain how I compromised the challenge Mindreader to a technical and non-technical crowd. If you are not a technical person in nature this will hopefully explain in a general sense how some of these attacks are pulled off.
> # Can you read my mind?
> # Challenge running at [https://mindreader.web.ctfcompetition.com/](https://mindreader.web.ctfcompetition.com/)

Mindreader is a web application running on the above link. The application accepts input from a user in a text box on a web form and returns a result.

![Mindreader](https://cdn-images-1.medium.com/max/2000/1*lSwNePc0d9eJNr7fNAl13g.png)

Inputting test data such as the obvious answer your mind to the question Hello, what do you want to read? results in an error.

![I cannot read ‘your mind’](https://cdn-images-1.medium.com/max/2000/1*N1UpBrnYjqdrt67PMrwNbw.png)

From my background in computer security I knew that sometimes these web applications are actually reading files that are stored on the web server. One of the things I tried answering with was /etc/passwd. For those of you not familiar with the Linux operating system, this file contains all the usernames and sometimes passwords for users on the system. Being able to retrieve this file means a remote user could abuse the web application and use it to read all kinds of local files on the web server. This could easily lead to further hacking and abuse.

![The /etc/passwd file contents](https://cdn-images-1.medium.com/max/2000/1*A0WzQ3KOMSLvPFog50KRfQ.png)

My answer of /etc/passwd worked and I am able to see the file contents. This Linux operating system uses another file /etc/shadow to contain more information about the users and passwords. I then attempted to see if I could retrieve that, and while I was successful, I am not able to gain any further information about the users or passwords on the system from the file. It seems we must hack it another way.

Since I can look at files by asking for them by name on the web application, I continue to ask for file names and directories that I know usually contain system information. In doing so I am able to see that some special directories contain information, but I am not able to access them. This is where the idea of *“Your Princess Is In Another Castle”* comes into play.

## The Princess

The goal of Capture The Flag is to obtain a digital “flag” to prove that you beat the challenge. A lot of times these flags are hidden in a certain way that an attacker must take certain pre-defined steps to get access to the flag — thereby proving they hacked and understand the system.

We all know the adage of Mario fighting his way through a castle and trying to find the Princess — only to be sorely disappointed by a misshapen headed fungus named Toad. While the example itself is old and cliché, it can be used to help explain the hack.

If we take the Princess in this example, but put her in a room in a maze like dungeon comprised of a bunch of rooms with doors and locks, we can visualize the next steps of the hack easier.

This maze represents a directory structure on a computer, with each room representing a different directory. On a Windows system C:\ would be a room in the dungeon, and one of the rooms you could unlock and go to would be C:\Program Files . On a Linux system /etc/ would be a room in the dungeon and /etc/init.d/ would be a room you could unlock and open.

![Dungeon Rooms representing a computer directory structure. Mario can move through these rooms if he has permission.](https://cdn-images-1.medium.com/max/2000/1*RmvPdBqNfxHTpxVniHcfag.jpeg)

I further tested the web application with a variety of directory names, or rooms in the dungeon to see what Mario had access to. Some of these doors were open and I was able to enter the room and see it’s contents, and others were locked with a different key or permissions. A special room /proc/environ was found, but Mario did not have the key to open the door.

At this point Mario needs to think about tricks and ways to navigate through the dungeon to get to the room that holds the Princess. From his knowledge working his way through the dungeon he knows that the room/proc/environ could be important, but he’s locked out. What else could he do in order to get into that room?

![The /proc/environ dungeon room — Noticed how it’s locked off](https://cdn-images-1.medium.com/max/2000/1*xyzqsTvJxB8Sae7DDM-_Ow.png)

One of the tricks that can be used is a concept in the Linux operating system called a symlink, or symbolic link. Symbolic links are very similar to shortcuts in a Windows operating system, as they virtually point to a file or directory. The difference with a symlink and a Windows shortcut is that the symlink actually represents the file itself instead of just being a shortcut to the file. A symlink could be used in conjunction with directory traversal (moving backwards through rooms in the dungeon) to try and hack into the room containing the Princess.

In order to understand how the symlink can be used to get access to the file, imagine another copy of the dungeon — **Dungeon B (Symlink Dungeon)**. When Mario moves from room to room in the original dungeon — **Dungeon A**— sometimes he will get to a special symlink room. When he enters this symlinked room he exists both in **Dungeon A**, but also in a different location in **Dungeon B**. The room in **Dungeon A **is symlinked to a different room in **Dungeon B**, but both spots represent the same location. This allows Mario access to rooms in **Dungeon B** that he could not get to in **Dungeon A**. It’s almost quantum and metaphysical in nature.

![By moving into the symlink room in Dungeon A, Mario effectively moves to the symlink room in Dungeon B. While both room are in different locations, they are essentially the same room.](https://cdn-images-1.medium.com/max/2000/1*XEVtQjOZB0e1v8uJFqURZw.png)

So now that we have a special locked room to look at in/proc/environ and a way to hack into that room using the symlink method, we can see if we can rescue the Princess.

Navigating to /dev/fd/ in **Dungeon A **shows that it’s a symlink to /proc/self/fd in **Dungeon B**. Mario uses the symlink trick and gets into a room located right after /proc/environ in **Dungeon B**. He’s so close, but the door to /proc/environ and possibly the Princess is locked behind him.

![Using the symlink trick Mario gets to a symlinked room in Dungeon B exactly 1 room ahead of /proc/environ — Where the Princess may be](https://cdn-images-1.medium.com/max/2000/1*G9WPkc70AQ6EHAqwa6_oRA.jpeg)

Mario needs to move backwards. Using a technique called **Directory Traversal (**placing ../ in a URL to indicate you want to move backwards a directory)**, **Mario can unlock the door behind him and rescue the Princess.

![Navigating to "/dev/fd/../environ" and retrieving the CTF flag from the environment variables](https://cdn-images-1.medium.com/max/2000/1*kZ8-UJqZTc2yeBb4LuEVxA.png)

/dev/fd/../environis inputted against Mindreader and the environ file is able to be read. The CTF flag hash is recovered and able to be reported for team points. The room with the Princess has been accessed and the rescue is complete! In this case the Princess is the FLAG variable.
>  FLAG = CTF{ee02d9243ed6dfcf83b8d520af8502e1}

![Mario moves back a room using Directory Traversal and is now located in /proc/environ through a symlink. The Princess is rescued and the game is won.](https://cdn-images-1.medium.com/max/2000/1*EFILrBB15krQXlLGfOZvYA.jpeg)

## **TL;DR — Technical**

I used a web application for local file read access to a directory traversed symlink /dev/fd/../environ in order to read environment variables — one of which contained the flag.

## TL;DR — Non-Technical

In non-technical terms I used a website against itself to let me read it’s files. Using two tricks I was able to access protected files, one of which contained a flag.

