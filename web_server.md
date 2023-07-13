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

## Level 2

For this level, we need to create a simple internet socket. We start by looking up the requirements for the sys_socket syscall. We see that ```rax``` needs to be 41, the family which is in ```rdi``` is 2, the type which is in ```rsi``` is 1 and the protocol in ```rdx``` is 0.

After setting all that up, we can then call ```syscall``. We then need to exit so we can pass 60 to ```rax``` and 0 to ```rdi```.

``` Assembly
.intel_syntax noprefix
.globl _start

.section .text

_start:
    mov rdi, 2
    mov rsi, 1
    mov rdx, 0
    mov rax, 41     # SYS_socket
    syscall
    mov rax, 60
    mov rdi, 0
    syscall

.section .data
```

## Level 3

For this level, we need to create a socket and then bind to it using syscalls. The majority of the code is the same from before:

``` Assembly
.intel_syntax noprefix
.globl _start

.section .text

_start:
    mov rdi, 2
    mov rsi, 1
    mov rdx, 0
    mov rax, 41     # SYS_socket
    syscall

    mov rdi, 3
    lea rsi, [rip+sockaddr]
    mov rdx, 16
    mov rax, 49
    syscall

    mov rdi, 0
    mov rax, 60
    syscall

.section .data
sockaddr:
    .2byte 2
    .2byte 0x5000
    .4byte 0
    .8byte 0
```

The big difference is the sockaddr block. This is basically creating the struct for the sockaddr_in that we can then pass to the bind syscall.

## Level 4

In this level, all we have to do is add a listen syscall before we exit the program

``` Assembly
.intel_syntax noprefix
.globl _start

.section .text

_start:
    mov rdi, 2
    mov rsi, 1
    mov rdx, 0
    mov rax, 41     # SYS_socket
    syscall

    mov rdi, 3
    lea rsi, [rip+sockaddr]
    mov rdx, 16
    mov rax, 49  # sys_bind
    syscall

    mov rdi, 3
    mov rsi, 0
    mov rax, 50  #sys_listen
    syscall

    mov rdi, 0
    mov rax, 60  # sys_exit
    syscall

.section .data
sockaddr:
    .2byte 2
    .2byte 0x5000  # sockaddr_in struct used in sys_bind
    .4byte 0
    .8byte 0
```

## Level 5

In this level, we need to implement the accept syscall. We can do so like this

``` Assembly
.intel_syntax noprefix
.globl _start

.section .text

_start:
    mov rdi, 2
    mov rsi, 1
    mov rdx, 0
    mov rax, 41     # SYS_socket
    syscall

    mov rdi, 3
    lea rsi, [rip+sockaddr]
    mov rdx, 16
    mov rax, 49  # sys_bind
    syscall

    mov rdi, 3
    mov rsi, 0
    mov rax, 50  #sys_listen
    syscall

    mov rax, 43
    mov rdi, 3
    mov rsi, 0
    mov rdx, 0
    syscall #sys_accept

    mov rdi, 0
    mov rax, 60  # sys_exit
    syscall

.section .data
sockaddr:
    .2byte 2
    .2byte 0x5000  # sockaddr_in struct used in sys_bind
    .4byte 0
    .8byte 0

sockaddr2:
    .2byte 2
    .2byte 0x5000
    .4byte 0
    .8byte 0
```

## Level 6

In this level, we need to respond to an http request. To do this, we need to accept the connection (last level), read in the request (sys_read), write the response (sys_write), and then close the file (sys_close). 

We also need to have the response string in the code as well which can be added under the data section using the ```.string``` syntax. 

``` Assembly
.intel_syntax noprefix
.globl _start

.section .text

_start:
    mov rdi, 2
    mov rsi, 1
    mov rdx, 0
    mov rax, 41     # SYS_socket
    syscall

    mov rdi, 3
    lea rsi, [rip+sockaddr]
    mov rdx, 16
    mov rax, 49  # sys_bind
    syscall

    mov rdi, 3
    mov rsi, 0
    mov rax, 50  #sys_listen
    syscall

    mov rax, 43
    mov rdi, 3
    mov rsi, 0
    mov rdx, 0
    syscall #sys_accept

    sub rsp, 1024
    mov rax, 0
    mov rdi, 4
    mov rsi, rsp
    mov rdx, 140
    syscall #sys_read

    mov rax, 1
    mov rdi, 4
    lea rsi, [rip+header]
    mov rdx, 19
    syscall #sys_write

    mov rax, 3
    mov rdi, 4
    syscall #sys_close

    mov rdi, 0
    mov rax, 60  # sys_exit
    syscall

.section .data
sockaddr:
    .2byte 2
    .2byte 0x5000  # sockaddr_in struct used in sys_bind
    .4byte 0
    .8byte 0

header:
    .string "HTTP/1.0 200 OK\r\n\r\n"
```

## Level 7

