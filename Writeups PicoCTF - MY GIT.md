Title: MY GIT
Category: General Skills
Difficulty: Easy

## Description
I have built my own Git server with my own rules! You can clone the challenge repo using the command below.

`git clone ssh://git@xebec.cylabacademy.net:31581/git/challenge.git`

Here's the password: `f64d4005`

Check the README to get your flag!

## Attempt
Clonned the challenge repo using the provided password. Take a look at the directory seems to only have a .git and a README.md files. looking at the git log also shows only one commit. branch is also only master.
![[Pasted image 20261014014858.png]]
Seems like I only need to push flag.txt using `root:root@academy`. Just change the git username and email I guess
```
git config user.name "root" && git config user.eamil "root@academy"
```
Create a flag.txt, commit and push to master.
![[Pasted image 20261014020309.png]]
Flag is printed in the git push output.

## Flag
academy{1mp3rs0n4t4_g17_345y_7d9222cd}