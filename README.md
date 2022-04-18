# THNB-CTF
Writeup for the THNB CTF , reverse categorie. only ELF files 

* baby_reverser
* brothersinarm
* packer
* toddler

# Baby_reverser

  After concating the content of the .txt file , we can clearly see that we are dealing with lines of assembly codes.


![alt text](https://i.imgur.com/U2OLBv9.png)


lets make an executable out of this lines.


![alt text](https://i.imgur.com/Dgwk6wM.png)


Using `nasm` command to form the object file, and specifying the format to be ELF; After that we link the object file and we get
Our executable.

Let's debug this little baby;

In assembly, the program start executing from the `_start` address, Its like the main function in c.


![alt text](https://i.imgur.com/rkFftjo.png)


After analysing it, Here's what this program does : 

  - Firstly : the program XOR the 24th character of our input buffer , with the one byte value "65" ,overwriting our input buff in that position by the         result , Then decrement "65" and `ECX` register value "23", by one and repeat the process until the value in the `ECX` Register equals -1. 

  - Secondly : We have another loop, It perform XOR operation again on our input buff after It got changed. And using a new 
        string to Xor : `^CBOU\033RPSQMjDRN\nHH\017OmBJA`
   the second loop checks the XOR result of every character , If Its not 0 it will print "Incorrect" and leave.

   If all the characters equals 0 after Xoring , then We get the winning message;

   ![alt text](https://i.imgur.com/QSZlR2w.png)

 Perfect, what we should do now is figure out the right input; 

 We know that the XOR operator is revirsible . so we only need to XOR the 2 strings;

 First one is the sequence of the byte 42 to the byte 65;

 ![alt text](https://i.imgur.com/BKiWFrc.png)

  `*+,-./0123456789:;<=>?@A"`

  Second one : `^CBOU\033RPSQMjDRN\nHH\017OmBJA` 

  Little c program to get the output .

  ![alt text](https://i.imgur.com/32FlTrC.png)

The flag;

![alt text](https://i.imgur.com/lNEkF40.png)






# brothersinarm

First lets try to execute this binary;


![alt text](https://i.imgur.com/8GGYd4X.png)


Not working

The file command telL us Its an ARM 32bit ELF;

Google time;

I ended up finding a way to execute this kind of binaries : 

`sudo apt install gcc-arm-linux-gnueabihf binutils-arm-linux-gnueabihf binutils-arm-linux-gnueabihf-dbg`

to execute : `qemu-arm -L /usr/arm-linux-gnueabihf ./brothersinarm`


![alt text](https://i.imgur.com/2XWndmY.png)



Reversing time;

I used ghidra for this one;


![alt text](https://i.imgur.com/nf51l59.png)


After taking a look at the code provided by ghidra , we can see that we have a big for loop, and a lot of if statements, bro hard coded it.

So heres how I Managed to solve this one :

  First we have "nbr" start with the value 0;

  If we check the end of the loop we see that the unlocked function get called after `nbr=0x539`;


![alt text](https://i.imgur.com/GLAZ6E0.png)


I went to see where nbr gets this value .


![alt text](https://i.imgur.com/Jd9gEPH.png)


Oh so if `nbr=0x5bb` and the character of our input is `d` , nbr gets that value and exit the loop. The letter `d` should be the last character of the input we are looking for;

But where he gets the  value `0x5bb` ??? 

I kept tracking nbr values by going on the reverse order of the loop .I ended up getting this : `PinkFl0yd`

lets check

![alt text](https://i.imgur.com/Z60CFn7.png)


ez








# Packer

Executing this one , It does nothing.

Trying to debug it with gdb , no main entry;

Since the name is Packer , I did some research about packing executables . I found that you can compress your ELF files, to make it hard to analyse and reverse, and also decrease the size of the executable.

Using the `upx` tool we can decompress it;

`upd -d packer`

![alt text](https://i.imgur.com/rimXUI5.png)


After compressing It We can find the main function now .

Lets take a look at the binary using gdb;


![alt text](https://i.imgur.com/Mxx7Bxf.png)


the program literally does nothing , but we can see that there are alot of  bytes being stored into our stack . 

I Copied those lines , put them in a file.

![alt text](https://i.imgur.com/p4sXfRJ.png)


And using linux commands I got the flag 






# toddler

This one is easy, Unfortunatly I deleted the binary but I still remember what can be done;

  After debugging , I notice that there is a string called "s3cret_message" , the program them compare every `byte - 10` of this string with the bytes
of our input, If they are all equal we get the flag;

SO basically, you just need to decrement 10 from every byte of that buffer and you will get the flag. 
