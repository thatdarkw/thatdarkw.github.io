---
title: Common Practice While Using Internet
date: 2022-05-04
categories: [Article]
tags: [article, internet, privacy]     # TAG names should always be lowercase
img_path: /assets/img/articles/common_practice/
---

# Summary -
Internet is huge but it has its pros and cons. With the increasing amount of scams, tracking on internet, I think it is very easy to fall for them if you don't follow the basics. In this post, I'll talk about some of the common practice to consider while using Internet. Ofcourse, its just my view and what I think is a good practice. So, if there's any mistake or anything to add on then do let me know.

# 1. Browser -
So, yeah, let's start with browser first. Below mentioned browsers are the ones which I prefer. You can customize other browsers as well...

### Firefox -
![](firefox.png)

My favourite browser... Why?

Its free and open source. (I know nearly all browsers are free so why Firefox?) Well, it collects very less data about you and that too can be disabled in settings. And it got ton of add-ons and themes to choose from. Once, you installed some extensions for adblocking and tracker blocking you should be good to go.

![](firefox_extension.png)
_Extensions which I've mentioned here are also available on Firefox Android. (Another reason why I love Firefox...)_

##### Extensions for Firefox -
**uBlock Origin** - From the official page, "uBlock Origin is not just an “ad blocker“, it's a wide-spectrum content blocker with CPU and memory efficiency as a primary feature."
uBlock Origin works great with blocking ads and stuff. It very rarely breaks any site at all. Also, its light on resources.

**Privacy Badger** - From the official page, "Privacy Badger is a browser extension that stops advertisers and other third-party trackers from secretly tracking where you go and what pages you look at on the web. If an advertiser seems to be tracking you across multiple websites without your permission, Privacy Badger automatically blocks that advertiser from loading any more content in your browser. To the advertiser, it’s like you suddenly disappeared."

**NoScript Security Suite** - Its optional... What this extension does is, it allows JavaScript and other potentially harmful content to be executed only by trusted web sites of your choice. So, its a good measure for Cross Site Scripting(XSS) attacks but it will probably break some sites.

**Dark Reader** - This one is also optional. What this extension does is, it darkens the page by using Javascript. You don't want to stare at white light for hours when browsing internet right? But sometimes this breaks sites so that's a con. But we get a dark mode for all the sites so that's a pro.


### Brave -
![](brave.png)

Brave browser is awesome as well. Brave is like a all in one browser which comes with adblocker and tracker blocker and some other nice stuff included so once you install it, you should be good to go. That's all. Its a whole package. Also, its a nice browser, based on Chromium as well. 

![](brave_vs_others.png)
_Comparison image from the official Brave Browser site..._

# 2. Use secure connection -
Since we talked about browser, let's see what is a secure connection.

Secure connections are designed to protect data sent between two computers via the Internet. For now, what you need to know is HyperText Transfer Protocol Secure (HTTPS) is the most widely used web communications protocol. If you look in the Address field of your web browser right now, it's likely you'll see "https://" at the front.

What's the difference between Hypertext Transfer Protocol(HTTP) and Hypertext Transfer Protocol Secure(HTTPS)?

You might have guessed it from the full form. HTTP requests and responses are sent in plaintext, which means that anyone can read them. HTTPS corrects this problem by using TLS/SSL encryption.

![](http-vs-https.svg)
_Comparison image from Cloudflare's site..._

There are still some sites on the internet which still use plain Hyper Text Transfer Protocol(HTTP). **So, while browsing, do not enter any personal or sensitive info on unsecured connection(HTTP). Look for https (not http) in the URL to indicate that there is a secure connection.**

**Remember, anything you do on HTTP connection is sent through plain text so if anyone is spying on you, they can clearly see what you are doing like what URL's you are visiting or what data you are sending to the website. Using a VPN can solve this but make sure you trust your VPN provider.** (More on VPN below.)

# 3. VPN -
Ah, VPN....

A Virtual Private Network (VPN) creates a secure network connection over a public network such as the internet and encrypts your traffic. Your ISP will continue to know that you are consuming bandwidth, but to their eyes, your traffic will be encrypted. Your real IP address will be hidden, so any server/network you connect to will only see your VPN's assigned IP address.

A paid VPN is recommended, as there are usually no good free ones -- such free VPNs will likely collect data from you, be too slow to bother with. The only free VPN I'd recommend is **ProtonVPN** which works fine for a free user. You just sometimes don't get speed and obviously premium servers are locked.

# 4. Be careful of what you download -
Only download software/apps or anything from a trusted source only. Try not to Google "how to download xyx for free" etc cause most of the times you'll be redirected to some sketchy sites which will show ton of ads and you may accidentally download and run trojan or malware on your system. It is also possible to make an executable look just like an image or a music file. So, be careful with that.

Remember, most of the time for a virus to work, it has to get executed first. So, make sure not to download and open anything from a sketchy source. If you are not sure about the downloaded file, try scanning it using anti-virus programs but remember they are not 100% effective.

# 5. Be aware of Phishing and scams -
Phishing is a type of social engineering where an attacker sends a fraudulent (e.g., spoofed, fake, or otherwise deceptive) message designed to trick a person into revealing sensitive information to the attacker or to deploy malicious software on the victim's infrastructure like ransomware.

Well, let's see it in example. 

Supposed you clicked on a sketchy link, and it takes you to a normal mail site login with strange url. You still went ahead and enter your username/password there and guess what? The person who's handling this sketchy site is no other than our little attacker. So, they got your creds. Oh no!!! (If something like this happens with you, just straight up change the password and use 2FA.)

Above method is not the only way to phish you, there are others as well. You can research them if you want to know more.

**Do not click on any random links especially when you get that link from a stranger.**

# 6. Use strong password -
Oh, password... How many of you use the one good'ol password for all the accounts? I know a lot are. (I've also done that so need to be embarassed or anything...) So, why not do that?

Suppose one site's data get compromised and you had account there. So, if you used same password for all other websites then guess what? Yeah, you probably guessed it. Your all accounts can be compromised. Imagine if that leak gets public? Oh no!!!

Also, try not to use simple passwords like yourname12, password123, summer2020, etc. Use something like complex which is probably more than 8 characters which contains alphabets, numbers and symbols and is not common like Passw0rd123, etc.

You might be saying now, yeah how can we remember all these complex password for all sites then?

Well, use a password manager. I'd recommend **BitWarden** which I've personally used and works great. Also, comes with password generator. (And no, I'm not sponsored...)

# 7. Enable 2FA -
2 Factor Authorization (2FA) or Multi-factor Authorization (MFA) is an electronic authentication method in which a user is granted access to a website or application only after successfully presenting two or more pieces of evidence (or factors) to an authentication mechanism: knowledge (something only the user knows like a password), possession (something only the user has like a phone), and inherence (something only the user is like a fingerprint).

So, even if someone knows your password, if you have 2FA enabled then guess what? They cannot get inside your account unless they pass the 2FA. Pretty nice eh?

**Also obviously, do not share your One Time Password (OTP) with anyone.**

# 8. Use up to date software -
Why? Out of date or not regularly updated operating systems and applications put you at risk because they have a lot of vulnerabilities. Many of these vulnerabilities can be easily detected and exploited.

Keeping softwares up to date is crucial to preventing malware infections and security breaches. Security patches, bug fixes, and new features are often included in software updates.

# Conclusion -
Hopefully, you found this helpful. Following the above things should improve your internet privacy and security. Do let me know what do you think? Again, this is just my view and what I think is a good practice. So, if there’s any mistake or anything to add on then do let me know.