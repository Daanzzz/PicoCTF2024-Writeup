# packer

**Points:** 100
<br>

## Tools Used
[IDA Free](https://hex-rays.com/ida-free/)<br>
[UPX](https://upx.github.io/)

## Description
> Reverse this linux executable?
 
## Hint
> What can we do to reduce the size of a binary after compiling it.

## Static analysis
The challenge provides us with an ELF-executable, without any instructions other than telling us to reverse it. Before starting, I will first try to run it to get some clue about its functionality.<br><br>
Let's give the ELF permissions to run first:<br>
```
$ chmod +x ./out
```
And now try to run it:<br>
```
$ ./out

Enter the password to unlock this file:
```
Hmm. Looks like we are supposed to provide a password to unlock the file, so I'll provide it with a random password to see what happens: <br>
```
aaa
You entered: aaa

Access denied
```
Just like expected. Now that I know the ELF's functionality, I'll run the command `strings` on the ELF to see if I can get any more details about what's going on behind the scenes:<br>
```
$ strings out
```
Looking at the command's output, I will focus on the last two lines:<br>
```
UPX!
UPX!
```
[UPX](https://upx.github.io/)? Does that mean that the ELF is actually packed? Well, that would match both the name of the challenge and the hint as well, so it makes sense.

## Solution
We've gathered enough information to suspect that the ELF is packed using UPX, so to confirm weather it is true or not, I'll try to unpack the ELF:<br>
```
upx -d out
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2023
UPX 4.1.0       Markus Oberhumer, Laszlo Molnar & John Reiser    Aug 8th 2023

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
    877724 <-    336512   38.34%   linux/amd64   out

Unpacked 1 file.
```
And... it worked! Just like expected.<br><br>

Now that the ELF is unpacked, I can finally open it on IDA and dive into the disassembly.<br>
I've been looking around the main function for a few moments and I've noticed this interesting code:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/a0bebdb5-1076-41ae-906c-9147e38311f8)
Looks like we've found the part where it checks weather the provided password is correct or not. But after taking an even closer look, I've noticed that the password is stored as a string in the program:<br>
```asm
aPasswordCorrec
```
In the following line: <br>
```asm
lea     rdi, aPasswordCorrec
```
<br>Great! Now I can see the password myself without even running the program. To do that, I'll double click on `aPasswordCorrec`, and that'll lead me here:
<br>![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/d59e96a0-db8f-47a7-8beb-fd801a9688d2)
It's our flag represented in a hexadecimal value, let's combine it into one hexadecimal value (first line with the 3rd):<br>
`7069636f4354467b5539585f556e5034636b314e365f42316e34526933535f35646565343434317d`
<br> Now all that's left to do is to turn it into ASCII, which will give us the final flag:
<br> `picoCTF{U9X_UnP4ck1N6_B1n4Ri3S_5dee4441}`

## Flag
`picoCTF{U9X_UnP4ck1N6_B1n4Ri3S_5dee4441}`
