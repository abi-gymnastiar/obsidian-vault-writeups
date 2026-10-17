## Description
It's mate in one. You know it, the engine knows it, my grandma knows it. The board says checkmate is one click away. The engine says no. Settle the argument.

You can access the web app from your AttackBox's browser via: `http://MACHINE_IP`

## Attempts
Let's start with a recon using `nmap -sV -sC 10.65.163.32`. While waiting, I also try port 80 by opening it on a browser. Seems like it's some sort of chess website called "EndgameTrainer"

![](../Image%20Trunk/Pasted%20image%2020261015223951.png)

The nmap results also only shows port 22 and 80 open.

![](../Image%20Trunk/Pasted%20image%2020261015224043.png)

Seems like the website runs on Node.js. For now, I should take a look around the website on a browser. Seems like it some sort of end-game chess scenario. We play as whites. 

Chess behaviors:
- We can move by dragging the white chess pieces
- The moves are logged inside the "Moves" section
- We can reset the positions with the "Reset position" button
- Moving the rook into a winning *checkmate* position does not work. There is a popup threatening to shut my pc down. lol.

Seems like the goal is for us to checkmate the opositon. Though the program does not like it when we do that on with rook.

I'm opening Burp Suite for this to make it easier for me to analyse the frontend behavior and intercept (if needed) the traffic.

![](../Image%20Trunk/Pasted%20image%2020261015225325.png)

Seems like the site is sending a HTTP request when we play a piece. Interestingly, when I play the rook a1 to a8 to checkmate the king, it throws me the threatening popup without ever sending a request. This means there is some sort of frontend validation to check my move before ever constructing a HTTP request. Client side validations are easy to bypass.

To bypass this, I just intercept a normal pawn move, then change the payload's body to move the rook instead.

![](../Image%20Trunk/Pasted%20image%2020261015225836.png)

And it works. We don't even need to know what kind of validation logic was behind that popup message.