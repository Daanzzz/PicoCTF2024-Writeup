# WinAntiDbg0x100  

**Points:** 200
<br>
**ZIP Password:** picoctf
<br>

## Tools Used
[IDA Free](https://hex-rays.com/ida-free/)

## Description
> This challenge will introduce you to 'Anti-Debugging.' Malware developers don't like it when you attempt to debug their executable files because debugging these files reveals many of their secrets! That's why, they include a lot of code logic specifically designed to interfere with your debugging process.
Now that you've understood the context, go ahead and debug this Windows executable!
This challenge binary file is a Windows console application and you can start with running it using cmd on Windows.
Challenge can be downloaded here. Unzip the archive with the password picoctf
 
## Hint
> Hints will be displayed to the Debug console. Good luck!

## Static analysis
After unzipping, we'll see two files:
<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/b1a2efed-d632-4fd8-8cb1-d95fab3c9643)
<br>
<br>
The exe looks nice, let's try to run it:
<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/ac5abc85-3d89-4c43-9bbd-eae793ed49f7)
<br>
Hmm, looks like it wants to be ran on a debugger. Noted.
<br>
<br>
I then tried to open the config.bin file, because it might've contained something useful, but that wasnt really the case:
<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/62166942-6d6b-4110-bdd0-97ce24c60035)
<br>
<br>
After seeing that the config.bin file has no use, I'm gonna head back to analyzing the exectuable.<br>
So, I opened IDA and ran the program (without breakpoints), and the output is now different:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/1a8926bd-0fae-453a-94c4-56d052a7f1d8)
<br>
<br>
Let's take a look at the first message:<br>
Level 1: Why did the clever programmer become a gardener? Because they discovered their talent for growing a ```'patch'``` of roses!<br><br>
Looks like the hint that was promised was given, and now I know that the way to solve this this challenge is to patch the bytes.

## Solution
After taking a quick look at the disassembly of the `main` function, I've found the code that's checking if a debugger is active:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/114d5374-4220-48e9-8752-4cc40fd8bd8b)
<br><br>
I'm gonna focus on this line:<br>
```asm
jz  short loc_5A161B
```
<br> In simpler terms, if the value in the `eax` register is zero, the program will jump to `loc_5A161B`, which doesn't happen right now. <br>
But what if I change `jz` to `jnz`, so it would jump to `loc_5A161B` if `eax` is *NOT* zero?<br><br>
To do that, I will have to patch the bytes. First select the `jz` instruction with the mouse, then click on `Edit`->`Patch Program`->`Change Byte`, and select the first byte:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/045890a0-db29-486f-8546-0d23f5929ea9)<br>
Change `0x74` (which represents the `jz` instruction) to `0x75` (which represents the `jnz` instruction), click on ok, and then click on `Edit`->`Patch Program`->`Apply Changes` to apply the changes to the program. <br>
It should look like this after saving:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/ae093d72-c7a4-4448-98fc-c0250e102527)
<br><br>
Now after re-running the program on IDA, we get the flag :)<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/bee5de50-67f3-4312-bbe1-b8d0af14448f)<br>
## Flag
`picoCTF{d3bug_f0r_th3_Win_0x100_a3c3a8a5}`
