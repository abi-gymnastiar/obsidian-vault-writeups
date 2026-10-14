Title: Quizploit
Category: Binary Exploitation
Difficulty: Easy

## Description
Solve the quiz.

Download the source code to answer questions here.

Download the binary to answer questions here. Connect with the challenge instance here: `nc chatelaine.cylabacademy.net 20614`

## Attempt
![Pasted image 20261014212531.png](../Image%20Trunk/Pasted%20image%2020261014212531.png)

Seems like we just need to answer some questions regarding the binary that was given to us. I downloaded the binary as well. At this point in my journey, I am still not familiar with the ELF binaries and its structures. I know tools like `ghidra` can let me analyse the functions within it and some hexdump. Seems like the quiz program gives me some useful stuff to learn as well.\

Before answering any questions, I decided to run some commands first to see what kind of binaries we're dealing with. I also did a little research into pwntools. It is a python program / framework that is commonly used in exploit development and is very useful in CTFs. Maybe we need to use it here lateer on.

![Pasted image 20261014213427.png](../Image%20Trunk/Pasted%20image%2020261014213427.png)

## 1. Is this a '32-bit' or '64-bit' ELF? (e.g. 100-bit)
- Answer: 64-bit
- Explanation: By using `file` command to analyse the file.

![Pasted image 20261014214001.png](../Image%20Trunk/Pasted%20image%2020261014214001.png)

## 2. What's the linking of the binary? (e.g. static, dynamic)
- Answer: dynamic
- Explanation: By using `file` command to analyse the file.

![Pasted image 20261014214642.png](../Image%20Trunk/Pasted%20image%2020261014214642.png)

## 3. Is the binary 'stripped' or 'not stripped'?
- Answer: not stripped
- Explanation: By using `file` command to analyse the file.

![Pasted image 20261014214837.png](../Image%20Trunk/Pasted%20image%2020261014214837.png)

## 4. Looking at the vuln() function, what is the size of the buffer in bytes? (e.g. 0x10)
- Answer: 0x15
- Explanation: From the source code

![Pasted image 20261014220611.png](../Image%20Trunk/Pasted%20image%2020261014220611.png)

## 5. How many bytes are read into the buffer? (e.g. 0x10)
- Answer: 0x90
- Explanation: From the source code

![Pasted image 20261014221333.png](../Image%20Trunk/Pasted%20image%2020261014221333.png)

## 6. Is there a buffer overflow vulnerability? (yes/no)
- Answer: yes
- Explanation: From quick research, it is overwriting the buffer beyond its allocated size (0x15). This allows attackers to change the return address or other stack data. This is called a buffer overflow attack.

![Pasted image 20261014221638.png](../Image%20Trunk/Pasted%20image%2020261014221638.png)

## 7. Name a standard C function that could cause a buffer overflow in the provided C code.
- Answer: fgets
- Explanation: if the number of bytes read exceeds the buffer. Which in this case it does.

![Pasted image 20261014222133.png](../Image%20Trunk/Pasted%20image%2020261014222133.png)

## 8. What is the name of function which is not called any where in the program?
- Answer: win
- Explanation: From the source code. It just declared but not used.

![Pasted image 20261014222231.png](../Image%20Trunk/Pasted%20image%2020261014222231.png)

## 9. What type of attack could exploit this vulnerability? (e.g. format string, buffer overflow, etc.)
- Answer: buffer overflow
- Explanation: from the question 0x6.

![Pasted image 20261014222407.png](../Image%20Trunk/Pasted%20image%2020261014222407.png)

## 10. How many bytes of overflow are possible? (e.g. 0x10)
- Answer: 0x7b
- Explanation: 0x90–0x15 = 0x7b.

![Pasted image 20261014222553.png](../Image%20Trunk/Pasted%20image%2020261014222553.png)

## 11. What protection is enabled in this binary?
- Answer: NX
- Explanation: Using `checksec file ./vuln` reveal NX is enabled. Quick research showed that NX (No-Execute) prevents executing code on the stack.

![Pasted image 20261014222904.png](../Image%20Trunk/Pasted%20image%2020261014222904.png)

## 12. What exploitation technique could bypass NX? (e.g. shellcode, ROP, format string)
- Answer: rop
- Explanation: Another quick research. ROP (Return-Oriented Programming) is a technique that chains existing code snippets (gadgets) to perform desired actions without executing code on the stack. Therefore bypassing NX protections.

![Pasted image 20261014223243.png](../Image%20Trunk/Pasted%20image%2020261014223243.png)

## 13. What is the address of 'win()' in hex? (e.g. 0x4011eb)
- Answer: 0x401176
- Explanation: From analysing the symbols within the binary using tools like binsider.

![Pasted image 20261014223336.png](../Image%20Trunk/Pasted%20image%2020261014223336.png)

## Flag
academy{my_bIn@4y_3xpl0it_fL@g_95fef02a}