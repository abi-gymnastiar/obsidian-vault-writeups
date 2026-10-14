Title: Old Sessions
Category: Web Exploitation
Difficulty: Easy

## Description
Proper session timeout controls are critical for securing user accounts. If a user logs in on a public or shared computer but doesn’t explicitly log out (instead simply closing the browser tab), and session expiration dates are misconfigured, the session may remain active indefinitely.

This then allows an attacker using the same browser later to access the user’s account without needing credentials, exploiting the fact that sessions never expire and remain authenticated. Your friend tells you to check out a new social media platform he built a few years ago. Although its still under development, he said the site is almost complete. He also mentioned that he hates constantly logging into sites, and so has made his page that 'once you login, you never have to log-out again'!

## Attempts
Provided with the link, I try to access it via browser. The hyperlink was http://chatelaine.cylabacademy.net:19657/ but the opened link redirected me to the `/login` page. Let's register an account.
![[Pasted image 20261014202909.png]]

![[Pasted image 20261014203001.png]]
From the comments, I notice the mention of `/sessions`. Appending it to the URL results in a list of the session history. One for `admin` and the other for `jimi`, which is my own session right now. There is also a variable `_permanent` which is set to be `true`. Interesting.
![[Pasted image 20261014203137.png]]
I tried copying the admin's session value into the browser's session inside cookie.
![[Pasted image 20261014203335.png]]
Navigating back to the `/` page results in me logged in as `admin`. The flag is also printed there as well.![[Pasted image 20261014203426.png]]

## Flag
academy{s3t_s3ss10n_3xp1rat10n5_3b59378b}

## Reflections
- It is important to set the session expirations.
- Not to store session history somewhere the user might have access to.