Title: SUDO MAKE ME A SANDWICH
Category: General Skills
Difficulty: Easy

## Description
Can you read the flag? I think you can! ssh -p 33869 ctf-player@xebec.cylabacademy.net using password 2eca92b9.

## Attempt
SSH into the provided host as the provided user. There is a `flag.txt` . Trying to read it results in a `Permission denied`. Checking with `sudo cat flag.txt` also results in a permission denied.

![Pasted image 20261014204308.png](../Image%20Trunk/Pasted%20image%2020261014204308.png)

We can check what can the user do using sudo privilege by using `sudo -l`. This results in the following:

![Pasted image 20261014204407.png](../Image%20Trunk/Pasted%20image%2020261014204407.png)

Well. I know `emacs` is a text editor program. Using it to see the content of `flag.txt` should work.

![Pasted image 20261014204524.png](../Image%20Trunk/Pasted%20image%2020261014204524.png)

Seems like using it thru ssh inside my `kitty` terminal emulator is a no no. I'll switch terminal and try it again.

![Pasted image 20261014204744.png](../Image%20Trunk/Pasted%20image%2020261014204744.png)

Found it. After embarrassingly struggles to close the editor (it's ctrl+x ctrl+c btw), plugging the flag into the answer results in a challenge completed.

## Flag
academy{ju57_5ud0_17_c3de3a9c}