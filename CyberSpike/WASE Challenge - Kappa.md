# Wase Challenge - Kappa
* **Event:** CyberSpike
* **Subtitle:** Gain remote access through vulnerability exploitation
* **Difficulty:** Expert
* **Categories:** Vulnerability, Exploitation, Remote Code Execution, PHP

## Description
In this lab your task is to investigate a supposted "unhackable" website to identify a vulnerability which can be exploited to gain remote access. 

Identify the vulnerability and exploit it to gain remote access to the server.

The hostname of the machine is kappa.lab and the IP address is **192.168.6.2**.
* **Identify** and **exploit** the vulnerability
* **Retrieve** the flag from the website's source code

## Environment
The challenge starts with a simple webpage with a single input for a PIN which can be submitted. Submitting a random PIN seems to give no feedback about the validation of the PIN.

## Solution
### Finding the exploit
At this point it is not clear what the exact vulnerability is that needs to be exploited. We need to investigate the website to find out what is going on.

The first step is to investigate the request that's being done to the backend when an PIN is submitted. We can see that the request is a POST, but there is no payload included. This means that no matter what we type in the input, we will never get feedback on our provided PIN.

When further investigating the POST request, we can see that the server returns the following headers:
* **X-Powered-By:** PHP/7.1.33dev
* **Server:** nginx/1.14.0 (Ubuntu)

These headers tell us a lot about the server it's running on. With this information we can find vulnerabilities that can be exploited on the server. Since this challenge is about Remote Code Execution, we can use Google (Or DuckDuckGo if you'd like) to find vulnerabilities that work on the given server and PHP versions. It seems that we can use [CVE-2019-11043](https://bugs.php.net/bug.php?id=78599) on this server.

Great, we found a vulnerability that we might be able to exploit. With a little more research we can find [this GitHub Repository](https://github.com/neex/phuip-fpizdam) which we can use to exploit the vulnerability. Luckily for us, the readme tells us exactly how to use the tool. We can use `go get github.com/neex/phuip-fpizdam` to download the source (And `snap install go --classic` to install Go).

Now that we have downloaded all the tools required to execute the exploit we can try to execute the exploit. We can start the exploit by running the following commands:
```
cd ~/go/src/github.com/neex/phuip-fpizdam
go run . "http://kappa.lab/index.php"
```

![](https://github.com/RobinvandenHurk/ctf-writeups/blob/master/CyberSpike/images/Wase%20Challenge%20-%20Kappa%20%5BExploit%201%5D.png "Shell output")
As we can see from the output of the script the exploit was executed successfully. That's great! We now know for sure this is the way to go. 

### Solving the challenge
Obviously the tool doesn't straight up give us the flag. It only tells us that the attack was successful and how it did it; by appending `?a=/bin/sh+-c+'which+which'&` to the URL. These parameters translate to `which which` in the shell.

At this point we are really close to retrieving the flag. All we need to do is alter the script to use a different command (to print the source code of the website), and return the response of the request. Both of these changes can be done in *attack.go*.

We need to modify the following lines:
```
22: checkCommand = `a=/bin/sh+-c+'cat+/var/www/html/index.php'&` // To print the contents of the index.php file
23: successPattern = "<?php" // This is what we expect in the request response to verify the success of the attack (Line 37)

After line 37: log.Printf(string(body)) // To print the request response when the successPattern is found in the body
```

Now that we have made the required changes to the script we can run it again (`go run . "http://kappa.lab/index.php"`). We can see that the content of index.php is prepended to the normal body of the request in which we can find the flag.
[](https://github.com/RobinvandenHurk/ctf-writeups/blob/master/CyberSpike/images/Wase%20Challenge%20-%20Kappa%20%5BResponse%20success%5D.png "Response")

## Conclusion
The misconfigured webserver gave us valuable information about the server which we used to find a CVE that could be used to exploit the server. Using a tool we found on GitHub we were able to execute our code on the remote server, which showed us the source of index.php (and thus the flag).
