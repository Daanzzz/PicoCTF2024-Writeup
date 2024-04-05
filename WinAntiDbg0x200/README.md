# WinAntiDbg0x200  

**Points:** 300
<br>
**ZIP Password:** picoctf
<br>

## Tools Used
[IDA Free](https://hex-rays.com/ida-free/)

## Description
> If you have solved WinAntiDbg0x100, you'll discover something new in this one. Debug the executable and find the flag!
This challenge executable is a Windows console application, and you can start by running it using Command Prompt on Windows.
This executable requires admin privileges. You might want to start Command Prompt or your debugger using the 'Run as administrator' option.
Challenge can be downloaded here. Unzip the archive with the password picoctf
 
## Hint
> Hints will be displayed to the Debug console. Good luck!

## Static analysis
Just like the previous challenge in the WinAntiDbg series, the ZIP comes with two files: config.bin and the program itself.
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/580cab28-a293-4043-bd84-0307ad9e84f2)
<br><br>

I first tried to launch the program through the terminal, and noticed that it needs to be ran as an Admin.
So, I'm gonna open IDA as administrator to be able to run and debug the exe.
<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/0c10d7be-7214-4d2d-800b-603bae15f925)
<br>
Just like the previous challenge, the debugger was detected. <br>
<br>
While looking around at the disassembly, I've noticed some interesting parts in the code:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/af9d2640-fa59-4b53-ab37-91d2562856bb)<br>

I will focus on `loc_FC1807`:
<br>
```asm
loc_FC1807:
push    offset aLevel2WhyDidTh ; "### Level 2: Why did the parent process"...
call    ds:OutputDebugStringW
push    3
call    sub_FC1090
add     esp, 4
call    sub_FC11D0
movzx   edx, al
test    edx, edx
jnz     short loc_FC1832
```

At the current state, it jumps to `loc_FC1832`:
```asm
loc_FC1832:
push    offset aOopsTheDebugge ; "### Oops! The debugger was detected. Tr"...
call    ds:OutputDebugStringW
jmp     loc_FC18CE
```
which just tells us that our debugger was detected.

## Solution
The first thing that came to my mind is to patch the bytes like in the previous challenge and change `jnz` to `jz`, so that the program may reach the part where it prints the flag.<br><br>
After patching, the program doesn't jump to `loc_41832`, but the code after detects the debugger once again:
```asm
call    ds:IsDebuggerPresent
test    eax, eax
jz      short loc_41847
```

I decided to once again, patch the bytes and change the `jz` in the code above to `jnz`, and then run to see what happens. <br>
And.. it worked!<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/e177e13e-f9c3-456d-ad9c-5a8810730a70)


## Flag
`picoCTF{0x200_debug_f0r_Win_603b1bdf}`

## Note
There's a more detailed explanation on how to patch bytes on IDA in the previous writeup of the WinAntiDbg challenges series.
