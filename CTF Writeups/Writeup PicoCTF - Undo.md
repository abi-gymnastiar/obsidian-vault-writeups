Title: Undo

Category: General Skills

Difficulty: Easy

## Description
"Can you reverse a series of Linux text transformations to recover the original flag? Start searching for the flag here `nc xebec.cylabacademy.net 14300`".

## Step 1
From the description, I tried to nc the service using the webconsole.

![Pasted image 20261014012136.png](../Image%20Trunk/Pasted%20image%2020261014012136.png)

Seems like just a standard Base64 encoding. Tried `base64 --help` to get the correct option for decoding, seems like it's base64 -d or --decode. Inputting the command below
```
echo "KXFuMm40NjA4LWZhMDFnQHplMHNmYTRlRy1nazNnLXRhMWZlcmlyRShsenJxbnBu" | base64 -d
```
results in incorrect answer. Turns out I just need to input `base64 -d` command as the answer.

## Step 2
Now I was given another task to reversed the text.

![Pasted image 20261014012741.png](../Image%20Trunk/Pasted%20image%2020261014012741.png)

I'm guessing it just need to reverse it. A quick googling "linux command for reversing string" shows the command is rev. so we can just pipe it into rev. Typing rev as the answer resutls in a correct answer.

## Step 3
Now I was given another task to replaced characters

![Pasted image 20261014013106.png](../Image%20Trunk/Pasted%20image%2020261014013106.png)

Another quick google search give me the command `tr` and `sed`. In this case, `tr` makes more sense since it's for individual characters. Answering with `tr '-' '_'` results in correct answer.

## Step 4
Now it's a similar chars replacement but form `()` into `{}`.

![Pasted image 20261014013801.png](../Image%20Trunk/Pasted%20image%2020261014013801.png)

turns out I don't need a second command for the closing bracket. Answering with `tr '()' '{}'` results in a correct answer.

## Step 5

![Pasted image 20261014014003.png](../Image%20Trunk/Pasted%20image%2020261014014003.png)

After a quick research, ROT13 is a simple cipher that shifts each letter by 13 positions. Another quick research, we can use `tr` again for this one. Answering with `tr 'A-Za-z' 'N-ZA-Mn-za-m'` results in a correct answer and reveal the flag:

![Pasted image 20261014014224.png](../Image%20Trunk/Pasted%20image%2020261014014224.png)

## Flag
The flag is `academy{Revers1ng_t3xt_Tr4nsf0rm@t10ns_8064a2ad}`.