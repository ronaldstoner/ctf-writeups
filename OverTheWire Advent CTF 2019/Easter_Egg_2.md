
## OverTheWire Advent Bonanza 2019 — Easter Egg 2

![](https://cdn-images-1.medium.com/max/10368/1*BtvumpW6IU8Vnb-QW7Ucjw.jpeg)

This write up is part of a bigger Capture The Flag competition. You can see other challenge write ups on the main post [here](https://medium.com/@forwardsecrecy/overthewire-advent-bonanza-2019-capture-the-flag-competition-66c50671c641).

Part of the fun of CTF challenges is searching for Easter Egg flags. These flags usually don’t require a ton of advanced skill, but are random fun things to find for points during an intense competition.

Easter Egg 2 gave no hints what-so-ever, except for that fact that it was hosted somewhere on the official OverTheWire Advent Bonanza 2019 website. This would be a search for a needle in haystack (or a flag on a website).

After a bunch of enumeration, a man in the middle web request proxy was used to intercept the web traffic between the web browser and the web server. By doing this, one can see every bit of information that is being sent when a user requests or sends information to a website.

A new an interesting web header was located using the MITM proxy.

![Ooooooh!](https://cdn-images-1.medium.com/max/2000/1*koY2KIeRnJcDEPnPxRTlog.png)

The string above should be recognized as **base64**, but it seems like it’s reversed. Reversing the text and decoding the **base64** string gives the following:

![Not the final flag, but we can still work with this](https://cdn-images-1.medium.com/max/2000/1*m9zYX49-aIwTa9C-SkQQFw.png)

Decoding the string does not reveal the final flag, but we do get text we can analyze and work with further. **ROT13** is a substitution cipher that literally moves letters forward or backwards a set number of positions, in this case 13 characters. An example of this would be the letter N in a message being written as the letter A (N to A is 13 letters). Using some linux command line fu, we can pipe the text into the **tr** command and reveal the final flag.

![ROT13 decrypt for the final flag](https://cdn-images-1.medium.com/max/2000/1*W0UFGcLji-08kDZZLNtVkQ.png)

