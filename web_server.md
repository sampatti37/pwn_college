# Web Server

## Setup

To run these challenges, you first need to run ```/challenge/run```. This will give you some starter code along with commands to run in order to format the code correctly as well as the command to test it in the challenge.

## Level 1

This is a simple level and all we need to do is write some assembly that exits a program or process. Fortunately, the starter code given in ```/challenge/run``` is exactly the code we need. Therefore our ```server.s``` file looks like this

``` Assembly
.intel_syntax noprefix
.globl _start

.section .text

_start:
    mov rdi, 0
    mov rax, 60     # SYS_exit
    syscall

.section .data
```

Now we can run ```as -o server.o server.s && ld -o server server.o``` and then ```/challenge/run ./server``` and get our flag. 
