# Assembly Crash Course

## Setup

For this module, we will start in the /challenge directory. There will be a file called ```run``` in there that can be executed by entering ```./run```.

This will print out the instructions for the level and what we need to do along with the values we may need for it. From there we will then write a python script to solve it.

First we need to install pwntools by running ```pip install pwntools```.

## Level 1

In this level, we want to set the value of a register. When we run the challenge, it asks us to set ```rdi``` to ```0x1337```. We can set up a python script for this. 

We need to ```import pwn``` and then construct a binary file of the assembly instructions we want to execute. We want to execute:

```Assembly
mov %rdi, $0x1337;
```

To do this in python, we can write:

```Python3
from pwn import *

p = process(['/challenge/./run'])


code = asm('mov rdi,0x1337',arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

The process line executes the /challenge/run file. The code is the byte-wise version of those assembly instructions. Finally, the send line sends our byte-wise code to the executing file. Interactive required in order for us to see the output from the file like the flag.

## Level 2

In this level, we are told we are doing addition. Looking at the ```run``` file, we see that we are supposed to add ```0x331337``` to ```rdi``` which is given to us preloaded with a certain value. 

We will use our script to solve this. All we need to do is replace the old ```mov``` instruction with ```add``` and with the proper parameters. 

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('add rdi,0x331337',arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 3

For this level, we are told to solve the equation ```f(x) = mx+b``` with ```m,x,b``` being ```rdi,rsi,rdx``` and storing the final answer in ```rax```. 

We can use either the ```mul``` instruction or the ```imul``` instruction. The ```imul``` instruction is much easier since it allows us to use two opperands as opposed to just one with the ```mul``` instruction. We can then write our script:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov    rax,rdi; 
    imul   rax,rsi; 
    add    rax,rdx;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 4

Now we need to do division. They tell us in the instruction after doing ```./run``` to use the ```div``` instruction, not the ```idiv``` instruction.

This means that we need to load ```rdi``` into ```rax``` and then run ```div rsi``` so that ```rax``` is set up properly. Our script now looks like:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov    rax,rdi; 
    div    rsi;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 5

Now we are doing the modulo opperation. From the previous challenge, we learned that the remainder is stored in ```rdx``` so after we do our division, we have to load ```rdx``` into ```rax``` like this:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov    rax,rdi; 
    div    rsi;
    mov    rax,rdx;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 6

For this level, we are introduced to the lower bit level registers such as 32, 16, and 8 bit registers. We are asked to compute the modulo of certain 64-bit registers using only the ```mov``` instruction

This can be done by just using the bit equivalent register and moving it to the desired register:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov    al,dil;
    mov    bx,si;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 7

For this level, we are introduced to bit shifts in assembly. We are asked to set the value of ```rax``` to the 5th LSB of ```rdi```. We can do that as such:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    int3;
    mov rax,rdi;
    shl rax,24;
    shr rax,56;
    int3;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

Here, we are multiplying each shift by 8 since each byte is 8 bits so we shift left by 3 bytes and then shift right all the way so that the value is in the last position.

The ```int3``` instruction prints out memory and register values and is helpful for debugging.

## Level 8

Now we are asked to use the bitwise ```and``` operation. We are asked to take the value of ```and rdi,rsi``` and store it in ```rax``` without using the ```mov``` instruction.

We can do this by running ```and rdi,rsi```. This will store that value in ```rdi```. Then, since ```rax``` is empty, anything 'anded' with 0's is itsself so we can do ```and rax,rdi``` and store that value in ```rax``` now:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    and rdi,rsi;
    and rax,rdi;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 9

This is a fun puzzle to figure out. We are given a random value in ```rdi``` and told to determine if it is even or odd and then set ```rax``` to ```1``` if its even and ```0``` if its odd.

We can start by doing ```and rdi,1```. This will give a ```1``` in ```rdi``` if the value is odd and a ```0``` if it is even. But now we need to flip that bit since those aren't the parameters specified for ```rax```. This means we need to do ```xor rdi,1``` to flip that bit and then we can store it in ```rax``` using the ```and``` trick.

Thus we get:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    and rdi,1;
    xor rdi,1;
    and rax,rdi;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 10

In this level, we are starting to work with memory address and dereferencing them. The write up for this level expalins how we can use and manipulate memory and what is stored in memory using the ```[]``` syntax. We are then asked to store the value at address ```0x404000``` into ```rax``` and then add ```0x1337``` to the value at address ```0x404000``` and store it at the same address.

We can modify our script to do the following:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov rax, [0x404000];
    mov rdi, [0x404000];
    add rdi, 0x1337;
    mov [0x404000], rdi;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 11

Now we are moving values from memory in different data sizes ranging from bytes to quad words. We can do this by moving the values from memory into different size registers like this:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov al, [0x404000];
    mov bx, [0x404000];
    mov ecx, [0x404000];
    mov rdx, [0x404000];
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 12

Now we are assigning values to memory addresses that are stored in registers. We get the hint in the write up to store the constant values in other registers rather than trying to assign large values to memory in one step. Using that hint, we can do the following:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov rdx, 0xdeadbeef00001337;
    mov rbx, 0xc0ffee0000;
    mov [rdi], rdx;
    mov [rsi], rbx;
    int3;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov rax, [rdi];
    mov rdx, [rdi+8];
    int3
    add rdx, rax;
    mov [rsi], rdx;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 14

This level begins to explain the concept of the stack and asks us to change the top value of the stack that is located a given memory address. We can do this by scripting:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    mov rax, [0x7fffff1ffff8];
    sub rax, rdi;
    mov [0x7fffff1ffff8], rax;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 15

In this level, we are tasked with swapping two values in registers using only the push and pop instruction. This can be done like this:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    push rdi;
    push rsi;
    pop rdi;
    pop rsi;
    int3;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 16

In this level, we need to read 4 values from the stack and then compute the average of them. To do this we need to use offsets to read the values from those addresses into registers.

Then we need to add all those registers together (I stored that value in ```rdi```). Lastly, we need to divide by 4 and ```push``` that value onto the stack. 

To divide by 4, we can simply just shift right by 2 on ```rdi``` rather than using the ```div``` instruction. Then we can do ```push rdi;``` to push it to the stack:

```Python3
from pwn import *

p = process(['/challenge/./run'])

code = asm('''
    add rdi, [rsp];
    add rdi, [rsp+8];
    add rdi, [rsp+16];
    add rdi, [rsp+24];
    shr rdi,2;
    push rdi;
    ''', arch = 'amd64',os = 'linux')

p.send(code)
p.interactive()
```

## Level 17

