# FactCheck  

**Points:** 200
<br>

## Tools Used
[BinaryNinja free](https://binary.ninja/)

## Description
> This binary is putting together some important piece of information... Can you uncover that information? Examine this file. Do you understand its inner workings? 

## Static analysis
We're given a file, so the first thing I'll do is check what type is it:<br>

```console
$ file bin
bin: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=ba87dd5805704ffe3d15a1e136c290a83fe95dba, for GNU/Linux 3.2.0, not stripped
```
Now we have some important information - this is an ELF binary. So I gave it permissions and tried to run it to see what it does:<br>

```console
$ chmod +x ./bin
$ ./bin
$ 
```
Well, looks like it doesn't really give us any output or ask for input, interesting. The next thing I did was checking the strings `strings ./bin`, but I didn't find anything helpful.<br>
At this point I've decided to open up BinaryNinja and see what's going on behind the scenes, and this is what I saw after decompiling the `main` function:<br>

```cpp
int32_t main(int32_t argc, char** argv, char** envp){
    void* fsbase
    int64_t rax = *(fsbase + 0x28)
    void var_249
    std::allocator<char>::allocator(this: &var_249)
    void var_248
    std::string::string(this: &var_248, __s: "picoCTF{wELF_d0N3_mate_")
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_228
    std::string::string(this: &var_228, __s: u"05d32aeb6c98…")
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_208
    std::string::string(this: &var_208, __s: &(*u"05d32aeb6c98…")[1])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_1e8
    std::string::string(this: &var_1e8, __s: &(*u"05d32aeb6c98…")[2])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_1c8
    std::string::string(this: &var_1c8, __s: &(*u"05d32aeb6c98…")[3])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_1a8
    std::string::string(this: &var_1a8, __s: &(*u"05d32aeb6c98…")[4])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_188
    std::string::string(this: &var_188, __s: &(*u"05d32aeb6c98…")[5])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_168
    std::string::string(this: &var_168, __s: &(*u"05d32aeb6c98…")[5])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_148
    std::string::string(this: &var_148, __s: &(*u"05d32aeb6c98…")[6])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_128
    std::string::string(this: &var_128, __s: &(*u"05d32aeb6c98…")[6])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_108
    std::string::string(this: &var_108, __s: &(*u"05d32aeb6c98…")[2])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_e8
    std::string::string(this: &var_e8, __s: &(*u"05d32aeb6c98…")[7])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_c8
    std::string::string(this: &var_c8, __s: &(*u"05d32aeb6c98…")[6])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_a8
    std::string::string(this: &var_a8, __s: &(*u"05d32aeb6c98…")[8])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_88
    std::string::string(this: &var_88, __s: &(*u"05d32aeb6c98…")[9])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_68
    std::string::string(this: &var_68, __s: &(*u"05d32aeb6c98…")[0xa])
    std::allocator<char>::~allocator(this: &var_249)
    std::allocator<char>::allocator(this: &var_249)
    void var_48
    std::string::string(this: &var_48, __s: u"8…")
    std::allocator<char>::~allocator(this: &var_249)
    if (*std::string::operator[](this: &var_208, __pos: 0) s<= 0x41 != 0)
        std::string::operator+=(this: &var_248)
    if (*std::string::operator[](this: &var_a8, __pos: 0) != 0x41 != 0)
        std::string::operator+=(this: &var_248)
    int32_t rax_10
    rax_10.b = sx.d(*std::string::operator[](this: &var_1c8, __pos: 0)) - sx.d(*std::string::operator[](this: &var_148, __pos: 0)) == 3
    if (rax_10.b != 0)
        std::string::operator+=(this: &var_248)
    std::string::operator+=(this: &var_248)
    std::string::operator+=(this: &var_248)
    if (*std::string::operator[](this: &var_168, __pos: 0) == 0x47 != 0)
        std::string::operator+=(this: &var_248)  std::string::operator+=(this: &var_248)
    std::string::operator+=(this: &var_248)
    std::string::operator+=(this: &var_248)
    555555555847      std::string::operator+=(this: &var_248)
    std::string::operator+=(this: &var_248, __c: 0x7d)  std::string::~string(this: &var_48)
    std::string::~string(this: &var_68)
    std::string::~string(this: &var_88)
    std::string::~string(this: &var_a8)
    std::string::~string(this: &var_c8)
    std::string::~string(this: &var_e8)
    std::string::~stri
    std::string::~string(this: &var_128)
    std::string::~string(this: &var_148)
    std::string::~string(this: &var_168)
    std::string::~string(this: &var_188)
    std::string::~string(this: &var_1a8)
    std::string::~string(this: &var_1c8)
    std::string::~string(this: &var_1e8)
    std::string::~string(this: &var_208)
    std::string::~string(this: &var_228)
    std::string::~string(this: &var_248)
    if (rax == *(fsbase + 0x28))
        return 0
    __stack_chk_fail()
    noreturn
}
```

## Solution
I can now see what's going on in the code but I don't really want to get deep into it, instead what I'd rather do is to just read the flag directly without making it even slightly complicated. In order to do that, I had thought about it, and as you can see in the code, a string is being built from all the first variables. But this same string will also be shown in the stack / one of the registers at some point in the execution of the code, and I've located this exact part:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/0b781625-e477-4c9b-aace-8ff79e8e3fea)<br>
As you can see, this is the last `operator+=`, which means that this is the last line that's building the string, aka the flag. So I ran the program and took a look at the register values once the code stopped at my breakpoint:<br>
![image](https://github.com/Daanzzz/PicoCTF2024-Writeup/assets/114262330/09b41235-cdb8-4abb-933a-8676fd544495)<br>
And as you can see there is the flag :)<br>

## Note
The last char `}` can be seen when moving to the next line after the breakpoint

## Flag
`picoCTF{wELF_d0N3_mate_e9da2c0e}`
