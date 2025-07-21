# Riyadh - Writeup
## Solve Time: ~15 mins
## Challenge Description:

<img width="791" height="671" alt="{922C5819-7D10-47D1-B225-52854D66C82D}" src="https://github.com/user-attachments/assets/092120d6-ecee-42eb-bf15-b0b41b944dac" />

## My Approach And Thought Process
- I ran Riyadh to check how it works, It prompted me to enter a "flag"

"challenge prompt"

- My First Instinct is to check the function that check the flag, maybe its strcmp and its insecure, maybe its a different kind of function
- Opening Ghidra, i find this ( for the sake of the writeup i will put the full code in 3 batches
```
bVar11 = 0;
  puVar9 = &buffer;
  for (lVar7 = 0x20; lVar7 != 0; lVar7 = lVar7 + -1) {
    *puVar9 = 0;
    puVar9 = puVar9 + 1;
  }
  Msg1((char *)&buffer);
  puts((char *)&buffer);
  puts("Compile Options: ${CMAKE_CXX_FLAGS} -O0 -fno-stack-protector -mno-sse");
  if (param_1 == 1) {
    Msg2((char *)&buffer);
    puts((char *)&buffer);
    uVar10 = 1;
  }
  else {
    CTFLearnHiddenFlag();
    __s2 = *(char **)(param_2 + 8);
    Msg3((char *)&buffer);
    iVar4 = strcmp((char *)&buffer,__s2);
```
- So we see that the first thing that the user should read is from the Msg1 function and then the "Compile Options: ${CMAKE....." so Msg1 accordingly should be: "Welcome to CTFlearn Riyadh Reversing Challenge!", we know this because we ran the program first, so that's 1 function down
Also looking at the code we see `CTFLearnHiddenFlag()`, but opening it reveals that it's sadly empty so its just a `Red Herring`( A Red Herring is something that is intentionally put to distract you from the main question )
Let's check the 2nd Function "Msg2" and it's Requirement (if param_1 == 1) means giving the program an input ( parameter) so it's probably the "You entered the wrong flag :-("


## Digging Deeper
- So now we know that our flag rests in one of the "Msg" Functions
```
iVar4 = strcmp((char *)&buffer,__s2);
      if (iVar4 == 0) {
      uVar10 = 2;
      Msg4((char *)&buffer);
      puts((char *)&buffer);
    }
    else {
      sVar5 = strlen(__s2);
      if (sVar5 == 0x1e) {
        iVar4 = 0;
        pcVar6 = (char *)operator.new[](0x100);
        Msg5(pcVar6);
        lVar7 = 0;
        do {
          pcVar1 = __s2 + lVar7;
          pcVar2 = pcVar6 + lVar7;
          lVar7 = lVar7 + 1;
          iVar4 = iVar4 + (uint)(*pcVar2 != *pcVar1);
        } while (lVar7 != 0x1e);
        pcVar6[0] = '\0';
        pcVar6[1] = '\0';
        pcVar6[2] = '\0';
        pcVar6[3] = '\0';
        pcVar6[4] = '\0';
        pcVar6[5] = '\0';
        pcVar6[6] = '\0';
        pcVar6[7] = '\0';
        pcVar6[0xf8] = '\0';
        pcVar6[0xf9] = '\0';
        pcVar6[0xfa] = '\0';
        pcVar6[0xfb] = '\0';
        pcVar6[0xfc] = '\0';
        pcVar6[0xfd] = '\0';
        pcVar6[0xfe] = '\0';
        pcVar6[0xff] = '\0';
        puVar9 = (undefined8 *)((ulong)(pcVar6 + 8) & 0xfffffffffffffff8);
//2nd batch of code as promised
```
- As we can see from the first line of this batch, it's a strcmp function which is an insecure function as we know it, so we might have to check it out in `gdb` ( if you dont know what gdb is, its a disassembler that shows everything that goes inside of the program, mostly used for dynamic and static analysis)

<img width="690" height="240" alt="image" src="https://github.com/user-attachments/assets/2ec86c59-e40c-4dd2-9d11-0692fa3a4d2f" />
 
- from what we can see from <+96> to <+103> this is where the whole Msg4 operation begins and ends, so lets check the register at <+101> to see what is getting compared so we put a breakpoint there using ` b *main +101 `

<img width="1035" height="251" alt="image" src="https://github.com/user-attachments/assets/6dfb4d6c-67bc-4568-b449-bced8d1a7753" />



- поздравляю! we found the flag... or so we thought. Turns out this is a fake flag that we could also see from doing a simple `ltrace` command

<img width="1289" height="250" alt="image" src="https://github.com/user-attachments/assets/6f60dfd3-fd81-434c-8515-1ac4327d6430" />


Now for Msg5.....

## The Solution
```
else {
      sVar5 = strlen(__s2);
      if (sVar5 == 0x1e) {
        iVar4 = 0;
        pcVar6 = (char *)operator.new[](0x100);
        Msg5(pcVar6);
```

- As we can see from Msg5, it checks for `strlen` only which is the string length, and the string needs to be 0x1e which is 30 chars long

- Let's try to check the registers after inputting a 30 char long string

<img width="805" height="237" alt="image" src="https://github.com/user-attachments/assets/12a55d34-3e92-4922-887c-f37d039489ee" />


ill put a breakpoint at the XOR instruction at +151 to see the final result in eax using b *main+151

<img width="807" height="222" alt="image" src="https://github.com/user-attachments/assets/445b7fbe-26a4-48a5-bf8f-2b90e2147e51" />


- And finally, (its the real flag dw lol), This challenge isn't very hard but it focuses on all the main beginner points ( ltrace,strings,gdb), Hopefully you loved this challenge and understood this writeup, Happy Hacking!

## What Did I learn From This Challenge
- Careful Reading of any code i come across
- More Understanding of how registers save data in memory
## Tools Used
- GDB ( with GEF plugin for QOL)/ Could Use R2
- ltrace
- Ghidra/ Could Use Cutter
- GDB plugin installation: https://github.com/hugsy/gef
