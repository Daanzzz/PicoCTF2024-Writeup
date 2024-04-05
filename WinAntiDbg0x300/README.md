# WinAntiDbg0x300  

**Points:** 400
<br>
**ZIP Password:** picoctf
<br>

## Tools Used
[IDA Free](https://hex-rays.com/ida-free/)<br>
[UPX](https://upx.github.io/)

## Description
> This challenge is a little bit invasive. It will try to fight your debugger. With that in mind, debug the binary and get the flag!
This challenge executable is a GUI application and it requires admin privileges. And remember, the flag might get corrupted if you mess up the process's state.
Challenge can be downloaded here. Unzip the archive with the password picoctf
If you get "VCRUNTIME140D.dll" and "ucrtbased.dll" missing error, then that means the Universal C Runtime library and Visual C++ Debug library are not installed on your Windows machine.
The quickest way to fix this is:
Download Visual Studio Community installer from https://visualstudio.microsoft.com/vs/community/
After the installer starts, first select 'Desktop development with C++' and then, in the right side column, select 'MSVC v143 - VS 2022 C++ x64/x86 build tools' and 'Windows 11 SDK' packages.
This will take ~30 mins to install any missing DLLs.

## Hints
> 1. There is an infinite loop to constantly check for the debugger.
> 2. Get past that infinite loop. Maybe 'Patch' the binary to jump to the appropriate location?
> 3. If you've done everything correctly, the flag should pop-up on your screen after 5 seconds of launching the program. The flag will also be printed to the Debug output to make it easy for you to copy the flag to the clipboard. See 'DebugView' program in Sysinternals Suite.

## Static analysis
This challenge is a bit more complicated than the previous ones in the series, it comes with a GUI:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/e627fa89-66e6-404e-bcf7-912ef905d541)
<br>
Looks interesting. I tried to open the program on IDA, but the disassembly was too complicated and I couldnt really get a clue on what's really going on other than seeing an infinite loop that's constantly checking for a debugger.
<br><br>

But then I've noticed something strange in the functions tab:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/a3982f30-54fb-4020-8b32-c00a5fa23601)
<br>
UPX? that gave me the idea that the program might've been packed using UPX.<br>

## Solution
So I tried to unpack the exe using UPX and... it worked! :<br>
```
> upx -d WinAntiDbg0x300.exe

                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2020
UPX 3.96        Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 23rd 2020

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
     75264 <-     26624   35.37%    win32/pe     WinAntiDbg0x300.exe

Unpacked 1 file.
```

Now that the exe is unpacked, I'm gonna open IDA and check the disassembly once again.<br>
Immediately after opening the exe in IDA, I've noticed this interesting function called `StartAddress_0` in the functions tab, and dived right into the assembly. And guess what - looks like this is the function where the flag is being printed.<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/aa24a33c-7051-4caa-bccc-933605353b73)
<br>
This will be useful for later so I've had it noted. <br>
<br>
Let's try to run the program on IDA:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/dc24817e-c255-421b-ad95-bbb172806062)
<br>
Just like I expected, the debugger was detected. This time I'm gonna try to find where the loop that's checking for a debugger is located.<br><br>
After a few moments, I've found the function that checks for the debugger: `WinMain`.<br>
Now that I know where the debugger checking is, I started to think about how can I jump from there (`WinMain`) to where the flag is printed, and then I've noticed that `StartAddress` is called from `WinMain`: <br>
```asm
.text:0040123F ; DWORD __stdcall StartAddress(LPVOID lpThreadParameter)
.text:0040123F StartAddress    proc near               ; DATA XREF: _WinMain@16_0+121↓o
.text:0040123F
.text:0040123F lpThreadParameter= dword ptr  4
.text:0040123F
.text:0040123F                 jmp     StartAddress_0
.text:0040123F StartAddress    endp
```
Here's the assembly code that calls `StartAddress` inside of `WinMain`:<br>
```asm
loc_40286C:             ; lpTableName
push    6Bh ; 'k'
mov     eax, [ebp+hInstance]
push    eax             ; hInstance
call    ds:__imp_LoadAcceleratorsW
mov     [ebp+hAccTable], eax
push    0               ; lpThreadId
push    0               ; dwCreationFlags
push    0               ; lpParameter
push    offset StartAddress ; lpStartAddress
push    0               ; dwStackSize
push    0               ; lpThreadAttributes
call    ds:__imp_CreateThread
mov     [ebp+hObject], eax
cmp     [ebp+hObject], 0
jnz     short loc_4028B9
```

From here, the solution is pretty easy. Here are the steps:<br>
First, I'll go to `loc_E727C6:` and change this line by patching the bytes:<br>
```asm
jz      short loc_E72802
```
into this line:<br>
```asm
jnz      short loc_E72802
```
That way, it's gonna change the program's flow so instead of exiting when a debugger is detected, it will continue the execution.<br>
What I'm gonna do now is go into `StartAddress`:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/26c11307-2e20-4e64-bd65-a8ab2c5a19f0)<br>
And focus on `loc_1837C0`:
```asm
loc_1837C0:
mov     eax, 1
test    eax, eax
jz      loc_1838E0
```
In the current state of the program, the `jz` instruction will jump into `loc_1838E0`, which will tell us that our debugger was detected. So, in order to bypass this I have to patch the bytes, once again, and change the hexadecimal from this: <br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/075ed449-bbf4-4db3-9372-67b1f46922f4)<br>
To that:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/9772320e-c364-4980-9e9b-a5a154871a53)<br>
(`0F 84` -> `0F 85`)<br>
Now that the I've changed `jz` to `jnz`, I'll try to run the program on IDA and see if I get the flag, and... it works :)<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/d07e0c58-5b22-4ff5-ac17-b8f46314a761)

## Flag
`PicoCTF{Wind0ws_antid3bg_0x300_aba8ee97}`

## Note
There's a more detailed explanation on how to patch bytes on IDA in the previous writeups of the WinAntiDbg challenges series.
