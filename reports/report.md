
# Overview of Buffer Overflow Attack Example Problems
#### Sabrina Pereira and Micah Reid


<!-- Content
Your project report should answer the following questions (note that some are the same as in the proposal and the update):

1) What is the goal of your project; for example, what do you plan to make, and what should it do?

2) What are your learning goals; that is, what do you intend to achieve by working on this project?

3) What resources did you find that were useful to you.  If you found any resources you think I should add to the list on the class web page, please email them to me.

4) What were you able to get done?  Include in the report whatever evidence is appropriate to demonstrate the outcome of the project.  Consider including snippets of code with explanation; do not paste in large chunks of unexplained code.  Consider including links to relevant files.  And do include output from the program(s) you wrote.

5) Explain at least one design decision you made.  Were there several ways to do something?  Which did you choose and why?

6) You will probably want to present a few code snippets that present the most important parts of your implementation.  You should not paste in large chunks of code or put them in the Appendix.  You can provide a link to a code file, but the report should stand alone; I should not have to read your code files to understand what you did.

7) Reflect on the outcome of the project in terms of your learning goals.  Between the lower and upper bounds you identified in the proposal, where did your project end up?  Did you achieve your learning goals?

Audience: Target an external audience that wants to know what you did and why.  More specifically, think about students in future versions of SoftSys who might want to work on a related project.  Also think about people who might look at your online portfolio to see what you know, what you can do, and how well you can communicate.

 You don't have to answer the questions above in exactly that order, but the logical flow of your report should make sense.  Do not paste the questions into your final report. -->





## Project Goals
Our goal is to understand the mechanics of buffer overflow attacks, and common issues that would make a program susceptible to this type of security risk. Our minimum goal is to go through the provided exercises to perform buffer overflow attacks (provided by Steve). Our reach goals are to understand buffer overflow attacks enough to create our own programs that either have exploitable vulnerabilities, or use practices to prevent buffer overflow attacks.


We chose to learn to use the CLion IDE and debugger to do this because both us wanted to learn about what debugging tools exist, what they look like, and what capabilities they afford us.

<!-- ## Learning Goals
We hope to gain experience using a debugger as this will allow us to gain more insight into how these attacks are performed, and what is actually happening in the memory. We hope to answer questions such as:
  - How does the data get copied into the buffer?
  - What types of programs are susceptible to this type of attack?
  - How would one go about performing this type of attack?
  - How do we know if our programs would be vulnerable? -->


## What is a Buffer Overflow Attack?

<!-- A buffer overflow condition exists when a program attempts to put more data in a buffer than it can hold or when a program attempts to put data in a memory area past a buffer. In this case, a buffer is a sequential section of memory allocated to contain anything from a character string to an array of integers. Writing outside the bounds of a block of allocated memory can corrupt data, crash the program, or cause the execution of malicious code.

A buffer overflow, or buffer overrun, is a common software coding mistake that an attacker could exploit to gain access to your system. To effectively mitigate buffer overflow vulnerabilities, it is important to understand what buffer overflows are, what dangers they pose to your applications, and what techniques attackers use to successfully exploit these vulnerabilities. -->

First, a buffer overflow occurs when a program tries to put data into a buffer that is of insufficient size for the operation. There is a specific block of memory that is allocated to storing that data, and going outside those bounds can cause various issues. If you know information about how these buffers are stored and what type of information is around them, you can intentionally cause a buffer overflow in a vulnerable program and modify its normal behavior.

This may include causing the program to crash, making the program skip to a different part of its instructions, or even opening a shell for the attacker.

Three resources we used to learn about buffer overflow attacks are the following:
  - [Computerphile video on Buffer Overflow Attack](https://www.youtube.com/watch?v=1S0aBV-Waeo)
  - [Veracode - What Is a Buffer Overflow?](https://www.veracode.com/security/buffer-overflow)
  - [OWASP - Buffer Overflow](https://owasp.org/www-community/vulnerabilities/Buffer_Overflow)



## How is a Buffer Overflow Attack Executed?

There are various ways memory can be overwritten to modify the program, but one common way, and the way that we will explore, is to overwrite the return address, as the location of it will be following the buffer to be written into as a way for the program to continue. The return address will be in a determinable location. The following examples go through our process in executing a buffer overflow attack in two different ways by doing this.


## Example 1: Jump to a Unauthorized Area in the Code
For this example, we will be using example code provided to us by Professor Steve Matsumoto that [can be found here](https://github.com/syclops/buffer-overflow-examples). In this example, we have a function `load_software` that should only be run if a user has an authorized 15-character key. However, the program has no bounds checking and this is what will allow us to overwrite the return address after the function to get the user's input.

Below is the `main()` function for this program:

```
int main() {
  puts("Enter your 15-character license key:");
  char key[BUFFER_SIZE];
  getbuf(key);
  if (validate(key)) {
    load_software();
    return 0;
  }
  return 1;
}
```



### Step 1: Figure how the data is stored in the buffer
To do this, made use of both the knowledge we learned in class, and learned how to use some of CLion's debugging tools, specifically the breakpoints and Memory View.

Looking at the `main()` function, we have a function `getbuf()` that will be put on the stack.

```
void getbuf(char* buf) {
  gets(buf);
}
```

This function `gets` fills the buffer from user standard input
entry.

![](images/getbuf_pre.png)

We see that once we process the input, the data is stored in the buffer from a lower memory address to a higher memory address.

![](images/getbuf_post.png)


### Step 2. Figuring out return address on the stack
From the Memory View, we can also see where the return address is on the stack. We can see that the address for `main` on the Frames pane is the same address that is 8 bytes from our buffer's end (including the null terminator) in little endian format.

![](images/return_addr.png)


- Stack stores return address 8 bytes from last array element

### Step 3. Figure out the address of `load_software`
To find out the memory address for the function we wanted to jump to, we used GDB's `info address`. In this example, we have the address `0x401e16`.

![](images/info_addr.png)


### Step 4. Overwriting return address
Using all of this information, we can overwrite the return address for `getbuf()` that stored in memory such that it looks something like this, inputting the desired address to jump to in little endian format:


![](images/overflow_mem_view_marked.png)

One way to do this is by running something like the following as was done in this example that allows you to input raw bytes using `\x` and see the result:

```
python3 -c 'import sys; sys.stdout.write("1111122222333330111222\x16\x1e\x40")' | ./foo
```
![](images/buffer_overflow_res.png)


## Example 2: Executing Shell Code
This example follows a similar structure to the previous, but instead of overwriting the buffer code such that the return address is set to jump the `load_software` function, we fill the buffer up with a sequence of bytes in our shellcode and have the return address jump to the start of that sequence.


This example will use the same [code as Example 1]((https://github.com/syclops/buffer-overflow-examples)) and shellcode found [here](http://shell-storm.org/shellcode/files/shellcode-603.php)). However, we will change the example code such that `BUFFER_SIZE = 64` as this example will require a given buffer large enough to fit our shellcode.




<!-- python3 -c 'import sys; sys.stdout.write( "\x90"*10 + "\x48\x31\xd2\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x48\xc1\xeb\x08\x53\x48\x89\xe7\x50\x57\x48\x89\xe6\xb0\x3b\x0f\x05" + "\xc4\x2f\x4c")' | ./foo -->


<!-- right function but wrong contents, has right shellcode -->

Sabrina stack address : 7fff ffffd8a0

\xff\x7f\x00\x00   \xa0\xd8\xff\xff

<!--
python3 -c 'import sys; sys.stdout.buffer.write( b"\x90"*10 + b"\x48\x31\xd2\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x48\xc1\xeb\x08\x53\x48\x89\xe7\x50\x57\x48\x89\xe6\xb0\x3b\x0f\x05" + b"\xc4\x2f\x4c")' > ex.txt -->





<!--
Submission Mechanics

1) In your project report, you should already have a folder called "reports" that contains a Markdown document called "update.md".  Make a copy of "update.md" called "report.md"

2) At the top of this document, give your report a title that at least suggests the topic of the project.  The title should not contain the name of the class or the words "project" or "report".

3) List the complete names of all members of the team.

4) Answer the questions in the Content section, above. Use typesetting features to indicate the organization of the document.  Do not include the questions as part of your document.

5) Complete your update, view it on GitHub, and copy the GitHub URL.  Then paste the URL in the submission space below.  You only need one report for each team, but everyone should submit it. -->
