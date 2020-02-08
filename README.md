# Challenge name
you_bee_ex
# Category
Malware Reverse Engineering
# Level
Hard
# Points
200
# Description
We thought that UPX can undo what it does, but we were wrong...
# Solution
We are given a 32bit pe file which is packed using UPX. 
![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/1.png)

Let's unpack the file and explore the code, the unpack can be done easyily using upx -d command.
After unpacking the file let's open the pe in ida and explore it.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/2.png)

Locking at the main function we found that he is checking the value of argc and if it's between 100 & 105 then retrive the full path of the file using GetModuleFileNameA API then xor each char from the path with a constant value from memory then print the encoded string.
So let's try passing 103 arguments to the pe file and see what's is the output then i try  the same arguments to the pack file.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/3.png)

So as we see same arguments in the packed and unpacked file but different results, after some debugging we will find that the program will crash in this instruction before entering the main function due to memory corruption from unpacking.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/4.png)

It make sense now so our plane is to find the OEP in the packed file then follow the code untill we are in the main.
To find the OEP we will search for a tail jump before alot of invaild instruction.
Let's open x64dbg if you scroll down you will find the tail jump which is in the address xxx4EA9.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/5.png)

Now let's go back for ida and set a break point in the jump at the same address and follow the code untill we found the main.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/6.png)

Let's run the program in local windows debugger in ida.
After one instruction from the breakpoint we will find our self in start function which is same as the unpacked one we checked before.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/7.png)

Comparing the unpacked file instructions with our location instructions we can determine easily the were to find main fucntion.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/8.png)

Now we are at the main function our goal now is to export the constant values that is being encoded with the full path.
The location of these values is in the EDX register as we can see.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/9.png)

After exporting the values we can easilly make a small script that do the same thing so we can retrive our flag.
But first we have to search in the strings on the packed file or the unpacked one to find a valid path or any string we can use to retrive our flag.
After viewing the strings in the unpacked file i found the original path of the application.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/10.png)

I wrote a small script to xor the exported values with tha path.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/11.png)

After runing the script we will find our flag.

![Image](https://github.com/0xN1ghtRa1d/Cybertalents/blob/you_bee_ex/12.png)

# Flag
flag{c4n_y0u_unp4ck_m4nu4lly_???}






