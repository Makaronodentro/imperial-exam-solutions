#Computer Systems 2015
##Section A
###1 a.

**(i).** Sign and Magnitude: find the positive binary and, if negative, flip the sign (first bit).

1010 1101

**(ii).** One's complement: find positive binary and, if negative, flip all of the bits.

1101 0010

**(iii).**  Two's complement: same as one's complement but if negative, add one to the one's complement.

1101 0011

**(iv).** -45 + 64 = 19

64 is the offset (excess-64), -45 is the number we want so we'll do the binary of 19.

0001 0011.

###1 b.

**High Order Interleaving** involves putting the module address bits first in the address
which results in having the adjacent addresses all come from the same module. If multiple memory
modules can be accessed concurrently then separate processors can access different rows simultaneously
allowing for parallel operation.

**Low Order Interleaving** involves putting the module address bits last in the address which results
in having the adjacent addresses all come from different modules.  This is good if the CPU can request
multiple adjacent memory locations.

###1 c.

![alt text](http://hyperphysics.phy-astr.gsu.edu/hbase/electronic/ietron/nor2.gif "Logo Title Text 1")

**(i)** Idempotent law A + A = A 

**(ii)** DeMorgan's law A + B = A' . B'

**(iii)** Negation

###1 d.

1. Convert to binary number: 11 0100.0100 1100 11...

2. Normalise: 1.1010 0010 0110 011.. x 2^5

3. Find Significand field (23 bits): 1010 0010 0110 0110 0110 011

4. Find Exponent field (5+127 = 132): 1000 0100

5. Sign: 1 

6. Put it all together... Sign + Exponent + Significand = 1 + 1000 0100 + 1010 0010 0110 0110 0110 011

| Binary | 1100 | 0010 | 0101 | 0001 | 0011 | 0011 | 0011 |0011 |
|--------|------|------|------|------|------|------|------|-----|
| Hex    | C    | 2    | 5    | 1    | 3    | 3    | 3    | 3   |

http://www.h-schmidt.net/FloatConverter/IEEE754.html

##Section B
###2 a.

**(i.)** Every machine instruction must have an OPCODE or operation code for selecting a CPU instruction (4 bits).

For more information see [CPU Organization](http://www.commsp.ee.ic.ac.uk/~kkleung/Computer_Systems_2015/3_Slides_CPUOrganisation.ppt) slides 6-8.



For general knowledge, the instruction format looks like this:

Opcode | Register | Address
---|----|----


**(ii.)** Three possible ways to address operands in an instruction:

1. **Register Operands:** both operands are stored in general purpose registers.

2. **Immediate Operands:** one operand is included in instruction as a constant.

3. **Memory Operands:** operand is stored in a memory address location.

Immediate addressing is the fastest because it doesn't require any look-up (although the other operand is usually a register or memory address then) but register addressing is faster than memory addressing because it doesn't require any memory. For more information and examples see slides 14-20 of [Pentium Architecture: Registers & Addressing Modes](http://www.commsp.ee.ic.ac.uk/~kkleung/Computer_Systems_2015/5_Pentium1-2.ppt) or check out this [page](http://www.tutorialspoint.com/assembly_programming/assembly_addressing_modes.htm).

**(iii.)** 

JZ will check the eflags register to see if zf is equal to 1. If it is then the program fetches the instruction located at memory address Address.

See [page 14](https://github.com/timothyylim/Imperial-College/blob/master/fall/computer-architecture/lecture-slides-comparch-part2/6_Pentium3.pdf)

An attempt at paraphrasing K. Leung's answer:

The instruction will be brought into the instruction register with the code. This is a jump zero instruction. We need to test if register i is 0 or not.  The control unit will check if this register i is 0. If it is zero then the address point of the instruction will be loaded into the program counter. You can also include information about the ALU which does the testing (comparison).

**(iv.)** CALL will always push the EIP so that we can return to where we were before the call using RET. Jump instructions do not save (push) the EIP register and therefore will not return to any location after jumping. Jump also typically has a conditional flag only allowing the program to jump to a different address if the flag contains a specific value (although an unconditional jump does exist). This can be used to create a nested function by jumping "backwards" and then subsequently executing calls in order from there ultimately leading back to the jump instruction. The jump instruction will, if used correctly, eventually be bypassed due to some condition which is also how while and for loops work. For more information on jump instructions and nested loops see [slides 14-21](http://www.commsp.ee.ic.ac.uk/~kkleung/Computer_Systems_2015/6_Pentium3.ppt).


a simpler explanation:

A JMP instruction permanently changes the program counter. A CALL instruction leaves information on the stack so that the original program execution sequence can be resumed.

- JMP performs a jump to a location, without doing anything else
- CALL pushes the current instruction pointer on the stack (rather: one after the current instruction), and then JMPs to the location. With a RET you can get back to where you were.

Jump acts like a GOTO whereas CALL acts more like a function call. 

For a JUMP:
        mov eip, SomeAddress

For a CALL:

on Entry:
        // Place the instruction location on the stack
        push eip
        
        // Store the CALLERs frame pointer and allocate space for the callee
        push ebp   || save the caller's stack base pointer register
        mov ebp, esp || set the functions base pointer equal to the caller's top
        sub esp, nbytes || allocate nbytes space for the callee's local variables
        
on Exit:
        // Reset the stack frame to the caller's
        mov esp,ebp
        pop ebp
        // Get the callers eip location
        pop eip


###2 b.
The following program will take an array A[] and reverse it. E.g. A = [1, 2, 3] will be come A = [3, 2, 1]. I'm going to use the following registers as such:

Register | Use
---|----
R0 | we don't need this actually
R1 | value of A at lower index
R2 | lower address of A
R3 | higher address of A

Address | Contents            | Pseudocode
--------|---------------------|-------
081H    | LOAD R2, 200H       | Load value of memory address 200H into R2
082H    | LOAD R3, 250H       | Load value of memory address 250H into R3
**083H**| **STORE R1, [R2]**  | **R1 = value of A at the lower memory address (temp)**
084H    | STORE R2, [R3]      | Value of R2 replaced with value of R3
085H    | STORE R3, [R1]      | 
086H    | INC R2              | point to the next lowest value
087H    | DEC R3              | point to the next highest value
**088H**| **JGT R2, R3, 083H**| **loop if R2 has passed R3**
089H    | STOP                | end the program
...     |                     |
200H    | A[0]                | holds A[0]
...     |                     |
250H*   | A[n-1]              | holds A[n-1]

*let's call 200H + n-1 = 250H (n=51).

K. Leung said we can assume that the first and last elements of the array are known so we can say  a[n-1] etc.

A different solution:

Address | Contents            | Pseudocode
--------|---------------------|-------
081H    | LOAD R0, 200H       | load A[0] into R0
081H    | LOAD R1, 250H       | load A[n] into R1
100H    |LOAD R2, [R0]        | load the value of A[0] into R2
        |LOAD R3, [R1]        | load the value of A[n] into R3
        |STORE R3, [R0]       | Set R0 to the value of R3
        |STORE R2, [R1]       | Set R1 to the value of R2
        |INC R0               | Increment the address of R0 (to A[1])
        |DEC R1               | Decrement the addres of R1 (to A[n-1])
        |JGT R1,R0,100H       | If address of R1 is greater than address of R0, keep looping 
        |STOP                 |  



##Section C
###3 a.

There will be four semaphores:

1. the client semaphore that is blocked while waiting for the device driver (initial value = 0)
2. the device driver has a semaphore which blocks on itself until the interrupt handler returns (initial value = 1)
3. mutex semaphore that controls access to the driver queue (initial value = 1)
4. driver process to block on waiting for requests (initial value = 0)

###3 b.
Pseudocode for the client process (modeled from the producer semaphore) would be:
```
while true do:
        generate a request containing the client sem and the response
        down(mutex)
        add request to driver queue
        up(mutex)
        up(wakeup)              // tell driver that there is a request
        down(clientsem)         // clientSem is now 0 and is initially 0 as it needs
                                to block after queuing the request
```

###3 c.
Pseudocode for driver process:
```
while true do:
        down(wakeup)                    // wait for a request
        down(mutex)
        remove item from driver queue
        up(mutex)
        enable interrupts from A to D hardware
        start A to D device
        down(devicesem)                 // waits for signal from interrupt handler
        consume_item (send to register)
        wait for notification from interrupt handler
        read data from device registers
        write data in response address // data is either A to D value or error
        up(clientsem)
                                        // The interrupt handler does the up on the devices
                                        to wake up the driver waiting for A to D to complete
```

###3 d.

###3 e i.

###3 e ii.

###4 a.

A translation Look-aside Buffer (TLB) is a memory cache (hardware) that stores the most recently used page numbers and their corresponding frame addresses for faster retrieval. It speeds up the process of using a page table so that we don't have to access the memory twice every time. See slide 26 from Memory Management or [this video](https://www.youtube.com/watch?v=95QpHJX55bM).

###4 b.

Memory Access Time = 200 ns (m)

TLB Access Time = 10 ns (e, epsilon)

Hit Ratio = 97% (a, alpha)

Three Level Paging System

Effective Access Time (EAT) = 2\*m + e - m\*a

Access Time for a TLB Miss (so a = 0) = 2\*m + e

**EAT** = 2\*200 + 10 - 200\*0.97 = **216 ns**

**Access Time for a TLB Miss** = 2\*200 + 10 = **410 ns** (set a = 0 because there is no hit)

See slide 28 from Memory Management.

###4 c.

Maximum Size of Address Space: 64 MB = 2^6 \* 2^20 = 2^26 bits

Page Size: 4096 Bytes = 4KB = 2^2 \* 2^10 = 2^12 bits

Maximimum Physical Memory Size: 16 MB = 2^4 \* 2^20 = 2^24 bits

**(i.)** A virtual address has **24 bits.**

**(ii.)** The maximum number of entries in a page table is 2^26 / 2^12 = 2^14 = **16384 entries.**

**(iii.)** The actual physical address translation for the virtual addresses:

* 13086: 13086 must be on page 1 which lies in frame 8 with an offset of 13086 % 4096 = 798. So, 13086 maps to physical address 8 \* 4096 + 798 = **33566.**
* 21200: 21200 must be on page 2 which lies in frame 4 with an offset of 21200 % 4096 = 720. So, 21200 maps to physical address 4 \* 4096 + 720 = **17104.**

How many bits are required for each page table entry?  There can be at most 2^24 / 2^12 = 2^12 frames in main memory, so a page table entry will require 12 bits for a frame number, plus protection and reference bits, etc.

Equations taken from memory management tutorial solutions.

###4 d.

Interactive Users require:

- Response time crucial = quick response to requests
- Meet users expectations = predictability

Background Jobs (batch systems, I guess) require:

- Throughput = maximize jobs per unit of time
- Turnaround time = minimize time between job submission and termination

A suitable process scheduling strategy would be a Multilevel Feedback Queue. 

A MFQ has the following characteristics:

- Run job on highest non-empty priority queue
- Each queue can use different scheduling algorithm
        - Usually round-robin
        - Could be different quantum eg highest priority is I/O bound with short quantum. Exceed quantum, then move down level but get bigger quantum.

This makes sense for our senario because we can specify short quantums for interactive processes so that the requirements of interactivity are met whilst specifying longer quantums for the background processes so they don't get in the way of user I/O but still have a chance to run.

See the Scheduling slides for more details. 
