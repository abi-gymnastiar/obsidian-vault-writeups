## Description
Practise your Linux skills and complete the challenges.

Answer the questions about the following files:

- 8V2L
- bny0
- c4ZX
- D8B3
- FHl1
- oiMO
- PFbD
- rmfX
- SRSq
- uqyw
- v2Vb
- X1Uy

The aim is to answer the questions as efficiently as possible.

## Finding the files
The files are scattered in the server. We can use `find` command to find those files. `find / -name "8V2L"` will recursively search the `8V2L` under `/` directory. We should take notes each locations:
- `/etc/8V2L`
- not found
- `/mnt/c4ZX`
- `/mnt/D8B3`
- `/var/FHl1`
- `/opt/oiMO`
- `/opt/PFbD`
- `/media/rmfX`
- `/etc/ssh/SRSq`
- `/var/log/uqyw`
- `/home/v2Vb`
- `/X1Uy`

## Which of the above files are owned by the best-group group(enter the answer separated by spaces in alphabetical order)

- Answer: D8B3 v2Vb
- Explanation:
  
  ![](../Image%20Trunk/Pasted%20image%2020261016215634.png)

## Which of these files contain an IP address?

- Answer: oiMO
- Explanation:
  
  ![](../Image%20Trunk/Pasted%20image%2020261016220310.png)
## Which file has the SHA1 hash of 9d54da7584015647ba052173b84d45e8007eba94

- Answer: c4ZX
- Explanation:
  ![](../Image%20Trunk/Pasted%20image%2020261016220628.png)
## Which file contains 230 lines?

- Answer: 
- Explanation:
  
  ![](../Image%20Trunk/Pasted%20image%2020261016220915.png)
  
  none of these are 230 lines. So by elimination, it must be the non-existent `bny0` file.
## Which file's owner has an ID of 502?

- Answer: X1Uy
- Explanation:
  
  ![](../Image%20Trunk/Pasted%20image%2020261016221445.png)
## Which file is executable by everyone?

- Answer: 8V2L
- Explanation:
  using `ls -lart`, we can see the action permissions for each file. `x` means executable.
  ![](../Image%20Trunk/Pasted%20image%2020261016221522.png)
