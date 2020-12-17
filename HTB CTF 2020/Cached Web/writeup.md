# Challenge Info
* **Event:**		Hack The Box University CTF 2020
* **Name:**			Cached Web
* **Creator:**		Makelaris
* **Difficulty:**	2/5
* **Category:**		Web

# Description
I made a service for people to cache their favourite websites, come and check it out! But don't try anything funny, after a recent incident we implemented military grade IP based restrictions to keep the hackers at bay...

# Writeup
* **Author:**		Robin van den Hurk

After reading the challenge description we visited the website to see what's going on. We were greeted with a minimal webpage, prompting to insert a URL. We tested the functionality by inserting `https://google.com` and got a response which was a screenshot of google.com. It seems like this webpage goes to the submitted website, makes a screenshot and displays that to us.

![](https://raw.githubusercontent.com/RobinvandenHurk/ctf-writeups/master/HTB%20CTF%202020/Cached%20Web/images/request_google.png "Screenshot of the initial request with Google url")

At this point we had no clue where the flag was located or how to get there. Luckily the source code of the challenge was also provided, allowing us to dig deeper. In here we found that there is an endpoint named /flag, which responds with an image (which probably contains the flag). We can also see that the endpoint can only be visited by localhost, just like the description claims.

From here it didn't take long to realize that we can simply host a simple webpage which contains `<img src="http://localhost:1337/flag">`. We could then make the bot request our webpage, which makes the bot load the flag on localhost, screenshot it and return the screenshot to us. This works because HTML runs client-side, so when the bot requests the resource at `localhost:1337/flag` it really just loads the file at /flag (Which we couldn't reach because of the IP whitelist) and displays it to us.

![](https://raw.githubusercontent.com/RobinvandenHurk/ctf-writeups/master/HTB%20CTF%202020/Cached%20Web/images/screenshot_flag.png "Flag")

(Thinking about this challenge again, I believe we did it the hard way. Unless there was a filter on the user input that blacklists any direct requests to localhost we could have just inserted `http://localhost:1337/flag` as the user input of the website. This would then simply load the image from localhost and showing us the screenshot)
