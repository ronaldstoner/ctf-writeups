
## #kksctf open 2019 — Postman

![](https://cdn-images-1.medium.com/max/2000/1*oFRt2vQe2fu79aGbi9q0jA.png)

## The Challenge
>  Hey, some kaсkers steal my mail. Can you help return and deliver it?

The Postman challenge provides us with a remote website and port. Navigating to the site shows us only a single line of text asking us to help the user retrieve their email.

## The Solution

Let’s help the user get their mail! Trying things like a mail. sub-domain unfortunately did not work. We will have to go back to basics. Checking the **robots.txt** file gives us an unlisted URL to check.

![robots.txt](https://cdn-images-1.medium.com/max/2000/1*YU5O1W8uU0IEs9SEnNtCGA.png)

We find the **/postbox** URL and navigate to it, but immediately get shut down due to an incorrect HTTP call.

![Denied](https://cdn-images-1.medium.com/max/2000/1*e7SCk6EV8UcLW-kGIsxCCA.png)

“Method Not Allowed” indicates that we made an incorrect type of request to the web server. By default, this call is a **GET** request. We can either change the request type using proxies or extensions in our browser, or we can use **curl** and send a **POST **request. While **GET** makes a call to retrieve information from a website, **POST** sends data (such as logging into a mailbox service!).
>  curl -X POST [http://tasks.open.kksctf.ru:8001/postbox](http://tasks.open.kksctf.ru:8001/postbox)

![The final flag](https://cdn-images-1.medium.com/max/2000/1*frTyyfA_7bQAzPQ3Bd7NSw.png)

The request is processed and the final flag is returned to us. Our user is now happy that they have their mail, and we are happy that this challenge is solved.

