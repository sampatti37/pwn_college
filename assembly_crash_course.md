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

