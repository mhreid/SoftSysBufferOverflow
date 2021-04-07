
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

## How is a Buffer Overflow Attack Executed?

There are various ways memory can be overwritten to modify the program, but one common way, and the way that we will explore, is to overwrite the return address, as the location of it will be following the buffer to be written into as a way for the program to continue. The return address will be in a determinable location. The following examples go through our process in executing a buffer overflow attack in two different ways by doing this.


## Example 1: Jump to a Unauthorized Area in the Code
In this example, we have a function `load_software` that should only be run if a user has an authorized 15-character key. However, the program has no bounds checking and this is what will allow us to overwrite the return address after the function to get the user's input.

### Step 1: Figure how the data is stored in the buffer
- looking at CLion's memory view
- Stepping through the instructions

### Step 2. Figuring out return address on the stack
- Stack stores return address 8 bytes from last array element

### Step 3. Figure out the address of `load_software`
- Used GDB `info`

### Step 4. Overwriting return address
- Steps to turn address into bytes

## Example 2: Executing Shell Code
This example follows a similar structure to the previous, but instead of overwriting the buffer code such that the return address is set to jump the `load_software` function, we fill the buffer up with a sequence of bytes in our shellcode and have the return address jump to the start of that sequence.


<!--
Submission Mechanics

1) In your project report, you should already have a folder called "reports" that contains a Markdown document called "update.md".  Make a copy of "update.md" called "report.md"

2) At the top of this document, give your report a title that at least suggests the topic of the project.  The title should not contain the name of the class or the words "project" or "report".

3) List the complete names of all members of the team.

4) Answer the questions in the Content section, above. Use typesetting features to indicate the organization of the document.  Do not include the questions as part of your document.

5) Complete your update, view it on GitHub, and copy the GitHub URL.  Then paste the URL in the submission space below.  You only need one report for each team, but everyone should submit it. -->
