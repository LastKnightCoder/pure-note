So far, we have only viewed computer systems down to the level of machine-language programs. We have seen that a processor must execute a sequence of instructions, where each instruction performs some primitive operation, such as adding two numbers. An instruction is encoded in binary form as a sequence of 1 or more bytes. The instructions supported by a particular processor and their byte-level encodings are known as its *instruction set architecture(ISA)*. Different "families" of processors, such as Intel IA32 and x86-64, IBM/Freescale Power, and the ARM processor family, have different ISAs. A program compiled for one type of machine will not run on another. On the other hand, there are many different models of processors within a single family. Each manufacturer produces processors of evergrowing performance and complexity, but the different models remain compatible at the ISA level. Popular families, such as x86-64, have processors supplied by multiple manufacturers. Thus, the ISA provides a conceptual layer of abstraction between compiler writers, who need only know what instructions are permitted and how they are encoded, and processor designers, who must build machines that execute those instructions.

In this chapter, we take a brief look at the design of processor hardware. We study the way a hardware system can execute the instructions of a particular ISA. This view will give you a better understanding of how computers work and the technological challenges faced by computer manufacturers. One important concept is that the actual way a modern processor operates can be quite different from the model of computation implied by the ISA. The ISA model would seem to imply sequential instruction execution, where each instruction is fetched and executed to completion before the next one begins. By executing different parts of multiple instructions simultaneously, the processor can achieve higher performance than if it executed just one instruction at a time. Special mechanisms are used to make sure the processor computes the same results as it would with sequential execution. This idea of using clever tricks to improve performance while maintaining the functionality of a simpler and more abstract model is well known in computer science. Examples include the use of caching in Web browsers and information retrieval data structures such as balanced binary trees and hash tables.

Chances  are  you  will  never  design  your  own  processor.  This  is  a  task  for experts working at fewer than 100 companies worldwide. Why, then, should you learn about processor design?

- It is intellectually interesting and important.There is an intrinsic value in learning how things work. It is especially interesting to learn the inner workings of a system that is such a part of the daily lives of computer scientists and engineers and yet remains a mystery to many. Processor design embodies many of the principles of good engineering practice. It requires creating a simple and regular structure to perform a complex task.
- Understanding how the processor works aids in understanding how the overall computer system works. In Chapter 6, we will look at the memory system and the techniques used to create an image of a very large memory with a very fast access time. Seeing the processor side of the processor–memory interface will make this presentation more complete.
- Although few people design processors,  many design hardware systems that contain processors. This has become commonplace as processors are embedded into real-world systems such as automobiles and appliances. Embedded-system designers must understand how processors work, because these systems are generally designed and programmed at a lower level of abstraction than is the case for desktop and server-based systems.
- You just might work on a processor design. Although the number of companies producing microprocessors is small, the design teams working on those processors are already large and growing. There can be over 1,000 people involved in the different aspects of a major processor design.

In this chapter, we start by defining a simple instruction set that we use as a running example for our processor implementations. We call this the "Y86-64" instruction set, because it was inspired by the x86-64 instruction set. Compared with  x86-64,  the  Y86-64  instruction  set  has  fewer  data  types,  instructions,  and addressing modes. It also has a simple byte-level encoding, making the machine code less compact than the comparable x86-64 code, but also much easier to design the CPU’s decoding logic. Even though the Y86-64 instruction set is very simple, it is sufficiently complete to allow us to write programs manipulating integer data. Designing a processor to implement Y86-64 requires us to deal with many of the challenges faced by processor designers.

We then provide some background on digital hardware design. We describe the basic building blocks used in a processor and how they are connected together and operated. This presentation builds on our discussion of Boolean algebra and bit-level operations from Chapter 2. We also introduce a simple language, HCL(for "hardware control language"), to describe the control portions of hardware systems. We will later use this language to describe our processor designs. Even if you already have some background in logic design, read this section to understand our particular notation.

As  a  first  step  in  designing  a  processor,  we  present  a  functionally  correct, but somewhat impractical, Y86-64 processor based on sequential operation. This processor executes a complete Y86-64 instruction on every clock cycle. The clock must run slowly enough to allow an entire series of actions to complete within onecycle. Such a processor could be implemented, but its performance would be well below what could be achieved for this much hardware.

With the sequential design as a basis, we then apply a series of transformations to create a pipelined processor. This processor breaks the execution of each instruction into five steps, each of which is handled by a separate section or stage of the hardware. Instructions progress through the stages of the pipeline, with one instruction entering the pipeline on each clock cycle. As a result, the processor can be executing the different steps of up to five instructions simultaneously. Making this processor preserve the sequential behavior of the Y86-64 ISA requires handling a variety of hazard conditions, where the location or operands of one instruction depend on those of other instructions that are still in the pipeline.

We have devised a variety of tools for studying and experimenting with our processor designs. These include an assembler for Y86-64, a simulator for running Y86-64 programs on your machine,  and simulators for two sequential and one pipelined processor design. The control logic for these designs is described by files in HCL notation. By editing these files and recompiling the simulator, you can alter and extend the simulator’s behavior. A number of exercises are provided that involve implementing new instructions and modifying how the machine processes instructions. Testing code is provided to help you evaluate the correctness of your modifications. These exercises will greatly aid your understanding of the material and will give you an appreciation for the many different design alternatives faced by processor designers.

## 4.1 The Y86-64 Instruction Set Architecture

Defining  an  instruction  set  architecture,  such  as  Y86-64,  includes  defining  the different components of its state,  the set of instructions and their encodings,  a set of programming conventions, and the handling of exceptional events.

### 4.1.1 Programmer-Visible State

As Figure 4.1 illustrates, each instruction in a Y86-64 program can read and modify some part of the processor state. This is referred to as the programmer-visible state, where the "programmer" in this case is either someone writing programs in assembly code or a compiler generating machine-level code. We will see in our processor implementations that we do not need to represent and organize this state in exactly the manner implied by the ISA, as long as we can make sure that machine-level programs appear to have access to the programmer-visible state. 

The state for Y86-64 is similar to that for x86-64. There are 15 program registers: `%rax`, `%rcx`, `%rdx`, `%rbx`, `%rsp`, `%rbp`, `%rsi`, `%rdi`, and `%r8` through `%r14`. (We omit the x86-64 register `%r15` to simplify the instruction encoding.) Each of these stores a 64-bit word. Register `%rsp` is used as a stack pointer by the push, pop, call, and return  instructions.  Otherwise,  the  registers  have  no  fixed  meanings  or  values. There are three single-bit condition codes, `ZF`, `SF`, and `OF`,  storing information about the effect of the most recent arithmetic or logical instruction. The program counter (PC) holds the address of the instruction currently being executed.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622141333295.png"  style="zoom:50%;" />

The memory is  conceptually  a  large  array  of  bytes,  holding  both  program and data. Y86-64 programs reference memory locations using virtual addresses. A combination of hardware and operating system software translates these into the actual, or physical, addresses indicating where the values are actually stored in memory. We will study virtual memory in more detail in Chapter 9. For now, we can think of the virtual memory system as providing Y86-64 programs with an image of a monolithic byte array.

A final part of the program state is a status code `Stat`, indicating the overall state of program execution. It will indicate either normal operation or that some sort  of exception has  occurred,  such  as  when  an  instruction  attempts  to  read from an invalid memory address. The possible status codes and the handling of exceptions is described in Section 4.1.4.

### 4.1.2 Y86-64 Instructions

Figure 4.2 gives a concise description of the individual instructions in the Y86-64 ISA. We use this instruction set as a target for our processor implementations. The set of Y86-64 instructions is largely a subset of the x86-64 instruction set. It includes only 8-byte integer operations, has fewer addressing modes, and includes a smaller set of operations. Since we only use 8-byte data, we can refer to these as "words" without any ambiguity. In this figure, we show the assembly-code representationof the instructions on the left and the byte encodings on the right. Figure 4.3 shows further details of some of the instructions. The assembly-code format is similar to the ATT format for x86-64.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622142231927.png" alt="image-20220622142231927" style="zoom:50%;" />

Here are some details about the Y86-64 instructions.

- The x86-64 movq instruction is split into four different instructions: `irmovq`, `rrmovq`, `mrmovq`, and `rmmovq`, explicitly indicating the form of the source and destination. The source is either immediate (i), register (r), or memory (m). It is designated by the first character in the instruction name. The destinationis either register (r) or memory (m). It is designated by the second character in the instruction name. Explicitly identifying the four types of data transfer will prove helpful when we decide how to implement them. 

    The memory references for the two memory movement instructions have a simple base and displacement format. We do not support the second index register or any scaling of a register’s value in the address computation.

  ```antd
  const { Popover } = antd
  const content = `为什么不支持将立即数直接传递到内存呢?`
  const Comment = () => {
    return (
    	<p>
    	  <Popover content={content}>
    	    <span className="comments blue">As with x86-64, we do not allow direct transfers from one memory location to another. In addition, we do not allow a transfer of immediate data to memory.</span>
    	  </Popover> 
    	</p>
    )
  }
  
  const root = ReactDOM.createRoot(el)
  root.render(<Comment />)
  ```

- There  are  four  integer  operation  instructions,  shown  in  Figure  4.2  as `OPq`. These are `addq`, `subq`, `andq`, and `xorq`. They operate only on register data, whereas x86-64 also allows operations on memory data. These instructions set the three condition codes `ZF`, `SF`, and `OF`(zero, sign, and overflow).

- The seven `jump` instructions (shown in Figure 4.2 as `jXX`) are `jmp`, `jle`, `jl`, `je`, `jne`, `jge`, and `jg`. Branches are taken according to the type of branch and the settings of the condition codes. The branch conditions are the same as with x86-64 (Figure 3.15).

- There are six conditional move instructions (shown in Figure 4.2 as `cmovXX`): `cmovle`, `cmovl`, `cmove`, `cmovne`, `cmovge`, and `cmovg`. These  have  the  same format as the register–register move instruction `rrmovq`, but the destination register is updated only if the condition codes satisfy the required constraints.

- The `call` instruction pushes the return address on the stack and jumps to the destination address. The `ret` instruction returns from such a call.

- The `pushq` and `popq` instructions implement push and pop, just as they do in x86-64.

- The `halt` instruction stops instruction execution. x86-64 has a comparable instruction, called `hlt`. x86-64 application programs are not permitted to use this instruction, since it causes the entire system to suspend operation. For Y86-64, executing the halt instruction causes the processor to stop, with the status code set to `HLT`. (See Section 4.1.4.)

### 4.1.3 Instruction Encoding

Figure 4.2 also shows the byte-level encoding of the instructions. Each instruction requires between 1 and 10 bytes, depending on which fields are required. Every instruction has an initial byte identifying the instruction type. This byte is split into two 4-bit parts: the high-order, or code, part, and the low-order, or function, part. As can be seen in Figure 4.2, code values range from 0 to 0xB. The function values are significant only for the cases where a group of related instructions share a common code. These are given in Figure 4.3, showing the specific encodings ofthe integer operation, branch, and conditional move instructions. Observe that `rrmovq` has the same instruction code as the conditional moves. It can be viewed as an "unconditional move" just as the `jmp` instruction is an unconditional jump, both having function code 0.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622145310017.png" alt="image-20220622145310017" style="zoom:50%;" />

As shown in Figure 4.4, each of the 15 program registers has an associated register identifier(ID) ranging from 0 to 0xE. The numbering of registers in Y86-64 matches what is used in x86-64. The program registers are stored within the CPU in a register file, a small random access memory where the register IDs serve as addresses. ID value `0xF` is used in the instruction encodings and within our hardware designs when we need to indicate that no register should be accessed.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622145719344.png" alt="image-20220622145719344" style="zoom:50%;" />

Some instructions are just 1 byte long, but those that require operands have longer encodings. First, there can be an additional register specifier byte, specifying either one or two registers. These register fields are called `rA` and `rB` in Figure4.2. As the assembly-code versions of the instructions show, they can specify the registers used for data sources and destinations, as well as the base register used in an address computation, depending on the instruction type. Instructions that have no register operands, such as branches and `call`, do not have a register specifier byte. Those that require just one register operand (`irmovq`, `pushq`, and `popq`) have the other register specifier set to value `0xF`. This convention will prove useful in our processor implementation.

Some instructions require an additional 8-byte constant word. This word can serve as the immediate data for `irmovq`, the displacement for `rmmovq` and `mrmovq` address specifiers, and the destination of branches and calls. ***Note that branch and call destinations are given as absolute addresses, rather than using the PC-relative addressing  seen  in  x86-64.***  Processors  use  PC-relative  addressing  to  give  more compact encodings of branch instructions and to allow code to be shifted from one part of memory to another without the need to update all of the branch target addresses. Since we are more concerned with simplicity in our presentation, we use absolute addressing. As with x86-64, ***all integers have a little-endian encoding.*** When the instruction is written in disassembled form, these bytes appear in reverse order.

As an example, let us generate the byte encoding of the instruction `rmmovq %rsp, 0x123456789abcd(%rdx)` in hexadecimal. From Figure 4.2, we can see that rmmovq has initial byte `40`. We can also see that source register `%rsp` should been coded in the `rA` field, and base register `%rdx` should be encoded in the `rB` field. Using the register numbers in Figure 4.4, we get a register specifier byte of `42`. Finally, the displacement is encoded in the 8-byte constant word. We first pad `0x123456789abcd` with leading zeros to fill out 8 bytes, giving a byte sequence of `00 01 23 45 67 89 ab cd`. We write this in byte-reversed order as `cd ab 89 67 45 23 01 00`. Combining these, we get an instruction encoding of `4042cdab896745230100`.

One important property of any instruction set is that the byte encodings must have a unique interpretation. An arbitrary sequence of bytes either encodes a unique instruction sequence or is not a legal byte sequence. This property holds for Y86-64, because every instruction has a unique combination of code and functionin its initial byte, and given this byte, we can determine the length and meaning of any additional bytes. This property ensures that a processor can execute an object-code program without any ambiguity about the meaning of the code. Even if the code is embedded within other bytes in the program, we can readily determine the instruction sequence as long as we start from the first byte in the sequence. On the other hand, if we do not know the starting position of a code sequence, we cannot reliably determine how to split the sequence into individual instructions. This causes problems for disassemblers and other tools that attempt to extract machine-level programs directly from object-code byte sequences.

### 4.1.4 Y86-64 Exceptions

The programmer-visible state for Y86-64 (Figure 4.1) includes a status code `Stat` describing the overall state of the executing program. The possible values for this code are shown in Figure 4.5. Code value 1, named `AOK`, indicates that the program is executing normally, while the other codes indicate that some type of exceptionhas occurred. Code 2, named `HLT`, indicates that the processor has executed a halt instruction. Code 3, named `ADR`, indicates that the processor attempted to read from or write to an invalid memory address, either while fetching an instruction or while reading or writing data. We limit the maximum address (the exact limit varies by implementation),  and any access to an address beyond this limit will trigger an `ADR` exception. Code 4, named `INS`, indicates that an invalid instruction code has been encountered.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622152248518.png" alt="image-20220622152248518" style="zoom:50%;" />

For  Y86-64,  we  will  simply  have  the  processor  stop  executing  instructions when it encounters any of the exceptions listed. In a more complete design, the processor would typically invoke an exception handler, a procedure designated to handle the specific type of exception encountered. As described in Chapter 8, exception handlers can be configured to have different effects, such as aborting the program or invoking a user-defined signal handler.

### 4.1.5 Y86-64 Programs

Figure 4.6 shows x86-64 and Y86-64 assembly code for the following C function:

```C
long sum(long *start, long count)
{
  long sum = 0;
  while (count) {
    sum += *start;
    start++;
    count--;
  }
  return sum;
}
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220622153100.png" alt="QQ截图20220622153100" style="zoom:50%;" />

The  x86-64  code  was  generated  by  the `gcc` compiler.  The  Y86-64  code  is similar, but with the following differences:

- The Y86-64 code loads constants into registers (lines 2–3), since ***it cannot use immediate data in arithmetic instructions.***
- The Y86-64 code requires two instructions (lines 8–9) to read a value from memory and add it to a register, whereas the x86-64 code can do this with a single `addq` instruction (line 5).
- Our hand-coded Y86-64 implementation takes advantage of the property that the `subq` instruction (line 11) also sets the condition codes, and so the `testq ` instruction of the gcc-generated code (line 9) is not required. For this to work, though, the Y86-64 code must set the condition codes prior to entering the loop with an `andq` instruction (line 5).

Figure  4.7  shows  an  example  of  a  complete  program  file  written  in  Y86-64 assembly code. The program contains both data and instructions. Directives indicate where to place code or data and how to align it. The program specifies issues  such  as  stack  placement,  data  initialization,  program  initialization,  and program termination.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220622154808.png" alt="QQ截图20220622154808" style="zoom:45%;" />

In this program, words beginning with `'.'` are assembler directives telling the assembler to adjust the address at which it is generating code or to insert somewords of data. The directive `.pos 0` (line 2) indicates that the assembler should begin generating code starting at address 0. This is the starting address for all Y86-64 programs. The next instruction (line 3) initializes the stack pointer. We can see that the label stack is declared at the end of the program (line 40), to indicate address `0x200` using a `.pos` directive (line 39). Our stack will therefore start at this address and grow toward lower addresses. We must ensure that the stack does not grow so large that it overwrites the code or other program data.

Lines 8 to 13 of the program declare an array of four words, having the values

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622191238757.png" alt="image-20220622191238757" style="zoom:40%;" />

The label array denotes the start of this array, and is aligned on an 8-byte boundary(using the `.align` directive). Lines 16 to 19 show a `"main"` procedure that calls the function sum on the four-word array and then halts.

As this example shows,  since our only tool for creating Y86-64 code is an assembler,  the  programmer  must  perform  tasks  we  ordinarily  delegate  to  the compiler,  linker,  and  run-time  system.  Fortunately,  we  only  do  this  for  small programs, for which simple mechanisms suffice.

Figure 4.8 shows the result of assembling the code shown in Figure 4.7 by an assembler we call `YAS`. The assembler output is in ASCII format to make it more readable. On lines of the assembly file that contain instructions or data, the object code contains an address, followed by the values of between 1 and 10 bytes.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220622191757.png" alt="QQ截图20220622191757" style="zoom:50%;" />

We have implemented an instruction set simulator we call `YIS`,  the purpose of which is to model the execution of a Y86-64 machine-code program without attempting to model the behavior of any specific processor implementation. This form of simulation is useful for debugging programs before actual hardware is available, and for checking the result of either simulating the hardware or running the  program  on  the  hardware  itself.  Running  on  our  sample  object  code, `YIS` generates the following output:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622193700780.png" alt="image-20220622193700780" style="zoom:50%;" />

The  first  line  of  the  simulation  output  summarizes  the  execution  and  the resulting values of the PC and program status. In printing register and memory values, it only prints out words that change during simulation, either in registers or in memory. The original values (here they are all zero) are shown on the left, and the final values are shown on the right. We can see in this output that register `%rax` contains `0xabcdabcdabcdabcd`,  the sum of the 4-element array passed to procedure sum. In addition, we can see that the stack, which starts at address `0x200` and grows toward lower addresses, has been used, causing changes to words of memory at addresses `0x1f0–0x1f8`. The maximum address for executable code is `0x090`, and so the pushing and popping of values on the stack did not corrupt the executable code.

## 4.2 Logic Design and the Hardware Control Language HCL

In hardware design, electronic circuits are used to compute functions on bits and to store bits in different kinds of memory elements. Most contemporary circuit technology represents different bit values as high or low voltages on signal wires. In current technology, logic value 1 is represented by a high voltage of around 1.0 volt, while logic value 0 is represented by a low voltage of around 0.0 volts. Three major components are required to implement a digital system: combinational logic to compute functions on the bits, memory elements to store bits, and clock signals to regulate the updating of the memory elements.

In this section, we provide a brief description of these different components. We  also  introduce  HCL  (for  "hardware  control  language"),  the  language  that we use to describe the control logic of the different processor designs. We only describe HCL informally here. 

### 4.2.1 Logic Gates

Logic gates are the basic computing elements for digital circuits. They generate an output equal to some Boolean function of the bit values at their inputs. Figure 4.9 shows the standard symbols used for Boolean functions `and`, `or`, and `not`. HCL expressions are shown below the gates for the operators in C (Section 2.1.8): `&&` for and, `||` for or, and `!` for not. We use these instead of the bit-level C operators `&`, `|`,  and `~`, because logic gates operate on single-bit quantities, not entire words. Although the figure illustrates only two-input versions of the `and` and `or` gates, it is common to see these being used as n-way operations for $n>2$. We still write these in HCL using binary operators, though, so the operation of a three-input and gate with inputs `a`,  `b`, and `c` is described with the HCL expression `a && b && c`.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622195750009.png" alt="image-20220622195750009" style="zoom:50%;" />

Logic gates are always active. If some input to a gate changes, then within some small amount of time, the output will change accordingly.

### 4.2.2 Combinational Circuits and HCL Boolean Expressions

By assembling a number of logic gates into a network, we can construct computational blocks known as combinational circuits.  Several restrictions are placed on how the networks are constructed:

- Every logic gate input must be connected to exactly one of the following: 
  1. one  of  the  system  inputs  (known  as  a primary  input)
  2. the  output connection of some memory element, or 
  3. the output of some logic gate.
- The outputs of two or more logic gates cannot be connected together. Otherwise, the two could try to drive the wire toward different voltages, possibly causing an invalid voltage or a circuit malfunction.
- The network must be acyclic. That is, there cannot be a path through a series of gates that forms a loop in the network. Such loops can cause ambiguity in the function computed by the network.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622200530803.png" alt="image-20220622200530803" style="zoom:50%;" />

Figure 4.10 shows an example of a simple combinational circuit that we will find useful. It has two inputs, `a` and `b`. It generates a single output `eq`, such that the output will equal `1` if either `a` and `b` are both `1` (detected by the upper `AND` gate) or are both `0` (detected by the lower `AND` gate). We write the function of this network in HCL as

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622200550519.png" alt="image-20220622200550519" style="zoom:50%;" />

This code simply defines the bit-level (denoted by data type `bool`) signal `eq` as a function of inputs `a` and `b`. As this example shows, HCL uses C-style syntax, with `'='` associating a signal name with an expression. Unlike C, however, we do not view this as performing a computation and assigning the result to some memory location. Instead, it is simply a way to give a name to an expression.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622223251291.png" alt="image-20220622223251291" style="zoom:50%;" />

Figure 4.11 shows another example of a simple but useful combinational circuit known as a multiplexor (commonly referred to as a "MUX"). A multiplexor selects a value from among a set of different data signals, depending on the value of a control input signal. In this single-bit multiplexor, the two data signals are the input bits `a` and `b`, while the control signal is the input bit `s`. The output will equal `a` when `s` is `1`, and it will equal `b` when `s` is `0`. In this circuit, we can see that the two and gates determine whether to pass their respective data inputs to the or gate. The upper and gate passes signal `b` when `s` is `0` (since the other input to the gate is `!s`), while the lower and gate passes signal `a` when `s` is `1`. Again, we can write an `HCL` expression for the output signal, using the same operations as are present in the combinational circuit:

```c
bool out = (s && a) || (!s && b);
```

Our HCL expressions demonstrate a clear parallel between combinational logic circuits and logical expressions in C. They both use Boolean operations to compute functions over their inputs. Several differences between these two ways of expressing computation are worth noting:

- Since a combinational circuit consists of a series of logic gates, it has the property that the outputs continually respond to changes in the inputs. If some input to the circuit changes, then after some delay, the outputs will change accordingly. By contrast, a C expression is only evaluated when it is encountered during the execution of a program.

- Logical expressions in C allow arguments to be arbitrary integers, interpreting 0 as false and anything else as true. In contrast, our logic gates only operate over the bit values 0 and 1.

- Logical expressions in C have the property that they might only be partially evaluated. If the outcome of an `AND` or `OR` operation can be determined by just evaluating the first argument, then the second argument will not be evaluated. For example, with the C expression

  ```c
  (a && !a) && func(b,c)
  ```

  the function `func` will not be called, because the expression `(a && !a)` evaluates to `0`. In contrast, combinational logic does not have any partial evaluation rules. The gates simply respond to changing inputs.

### 4.2.3 Word-Level Combinational Circuits and HCL Integer Expressions
By assembling large networks of logic gates, we can construct combinational circuits that compute much more complex functions. Typically, we design circuits that operate on data words. These are groups of bit-level signals that represent an integer or some control pattern. For example, our processor designs will contain numerous words, with word sizes ranging between 4 and 64 bits, representing integers, addresses, instruction codes, and register identifiers.

Combinational circuits that perform word-level computations are constructed using logic gates to compute the individual bits of the output word, based on the individual bits of the input words. For example, Figure 4.12 shows a combinational circuit that tests whether two 64-bit words A and B are equal. That is, the output will equal `1` if and only if each bit of A equals the corresponding bit of B. This circuit is implemented using 64 of the single-bit equality circuits shown in Figure 4.10. The outputs of these single-bit circuits are combined with an `AND` gate to form the circuit output.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622224627117.png" alt="image-20220622224627117" style="zoom:50%;" />

In HCL, we will declare any word-level signal as an int, without specifying the word size. This is done for simplicity. In a full-featured hardware description language, every word can be declared to have a specific number of bits. HCL allows words to be compared for equality, and so the functionality of the circuit shown in Figure 4.12 can be expressed at the word level as

```c
bool Eq = (A == B);
```

where arguments A and B are of type int. Note that we use the same syntax conventions as in C, where `'='` denotes assignment and `'=='` denotes the equality operator.

As is shown on the right side of Figure 4.12, we will draw word-level circuits using medium-thickness lines to represent the set of wires carrying the individual bits of the word, and we will show a single-bit signal as a dashed line.

Figure 4.13 shows the circuit for a word-level multiplexor. This circuit generates a 64-bit word Out equal to one of the two input words, A or B, depending on the control input bit `s`. The circuit consists of 64 identical subcircuits, each having a structure similar to the bit-level multiplexor from Figure 4.11. Rather than replicating the bit-level multiplexor 64 times, the word-level version reduces the number of inverters by generating `!s` once and reusing it at each bit position.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622224947338.png" alt="image-20220622224947338" style="zoom:50%;" />

We will use many forms of multiplexors in our processor designs. They allow us to select a word from a number of sources depending on some control condition. Multiplexing functions are described in HCL using case expressions. A case expression has the following general form:

```c
[
  select1 : expr1;
  select2 : expr2;
  ...
  selectk : exprk;
]
```

The expression contains a series of cases, where each case `i` consists of a Boolean expression `selecti`, indicating when this case should be selected, and an integer expression `expri`, indicating the resulting value.

Unlike the switch statement of C, we do not require the different selection expressions to be mutually exclusive. Logically, the selection expressions are evaluated in sequence, and the case for the first one yielding `1` is selected. For example, the word-level multiplexor of Figure 4.13 can be described in HCL as

```c
word Out = [
  s: A;
  1: B;
];
```

In this code, ***the second selection expression is simply 1, indicating that this case should be selected if no prior one has been.*** This is the way to specify a default case in HCL. Nearly all case expressions end in this manner.

Allowing nonexclusive selection expressions makes the HCL code more readable. An actual hardware multiplexor must have mutually exclusive signals controlling which input word should be passed to the output, such as the signals `s` and `!s` in Figure 4.13. To translate an HCL case expression into hardware, a logic synthesis program would need to analyze the set of selection expressions and resolve any possible conflicts by making sure that only the first matching case would be selected.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622225735191.png" alt="image-20220622225735191" style="zoom:50%;" />

The selection expressions can be arbitrary Boolean expressions, and there can be an arbitrary number of cases. This allows case expressions to describe blocks where there are many choices of input signals with complex selection criteria. For example, consider the diagram of a 4-way multiplexor shown in Figure 4.14. This circuit selects from among the four input words A, B, C, and D based on the control signals `s1` and `s0`, treating the controls as a 2-bit binary number. We can express this in HCL using Boolean expressions to describe the different combinations of control bit patterns:

```C
word Out4 = [
  !s1 && !s0 : A; # 00
  !s1 : B;        # 01
  !s0 : C;        # 10
  1 : D;          # 11
];
```

The comments on the right (any text starting with `#` and running for the rest of the line is a comment) show which combination of `s1` and `s0` will cause the case to be selected. Observe that the selection expressions can sometimes be simplified, since only the first matching case is selected. For example, the second expression can be written `!s1`, rather than the more complete `!s1 && s0`, since the only other possibility having `s1` equal to `0` was given as the first selection expression. Similarly, the third expression can be written as `!s0`, while the fourth can simply be written as `1`.

As a final example, suppose we want to design a logic circuit that finds the minimum value among a set of words A, B, and C, diagrammed as follows:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622225907489.png" alt="image-20220622225907489" style="zoom:50%;" />

We can express this using an HCL case expression as

```C
word Min3 = [
  A <= B && A <= C : A;
  B <= A && B <= C : B;
  1 : C;
];
```

Combinational logic circuits can be designed to perform many different types of operations on word-level data. The detailed design of these is beyond the scope of our presentation. One important combinational circuit, known as an arithmetic/logic unit (ALU), is diagrammed at an abstract level in Figure 4.15. 

In our version, the circuit has three inputs: two data inputs labeled A and B and a control input. Depending on the setting of the control input, the circuit will perform different arithmetic or logical operations on the data inputs. Observe that the four operations diagrammed for this ALU correspond to the four different integer operations supported by the Y86-64 instruction set, and the control values match the function codes for these instructions (Figure 4.3). Note also the ordering of operands for subtraction, where the A input is subtracted from the B input. This ordering is chosen in anticipation of the ordering of arguments in the `subq` instruction.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220622230013102.png" alt="image-20220622230013102" style="zoom:50%;" />

### 4.2.4 Set Membership
In our processor designs, we will find many examples where we want to compare one signal against a number of possible matching signals, such as to test whether the code for some instruction being processed matches some category of instruction codes. As a simple example, suppose we want to generate the signals `s1` and `s0` for the 4-way multiplexor of Figure 4.14 by selecting the high and low-order bits from a 2-bit signal code, as follows:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623092031217.png" alt="image-20220623092031217" style="zoom:50%;" />

In this circuit, the 2-bit signal code would then control the selection among the four data words A, B, C, and D. We can express the generation of signals `s1` and `s0` using equality tests based on the possible values of code:

```C
bool s1 = code == 2 || code == 3;
bool s0 = code == 1 || code == 3;
```

A more concise expression can be written that expresses the property that `s1` is `1` when code is in the set `{2, 3}`, and `s0` is `1` when code is in the set `{1, 3}`:

```C
bool s1 = code in { 2, 3 };
bool s0 = code in { 1, 3 };
```

The general form of a set membership test is

```C
iexpr in {iexpr1, iexpr2, ..., iexprk}
```

where the value being tested (`iexpr`) and the candidate matches (`iexpr1` through `iexprk`) are all integer expressions.

### 4.2.5 Memory and Clocking

Combinational circuits, by their very nature, do not store any information. Instead, they simply react to the signals at their inputs, generating outputs equal to some function of the inputs. To create sequential circuits—that is, systems that have state and perform computations on that state—we must introduce devices that store information represented as bits. Our storage devices are all controlled by a single clock, a periodic signal that determines when new values are to be loaded into the devices. We consider two classes of memory devices:

- Clocked registers (or simply registers) store individual bits or words. The clock signal controls the loading of the register with the value at its input.
- Random access memories (or simply memories) store multiple words, using an address to select which word should be read or written. Examples of random access memories include
  1. the virtual memory system of a processor, where a combination of hardware and operating system software make it appear to a processor that it can access any word within a large address space;
  2. the register file, where register identifiers serve as the addresses. In a Y86-64 processor, the register file holds the 15 program registers (`%rax` through `%r14`).

As we can see, the word "register" means two slightly different things when speaking of hardware versus machine-language programming. In hardware, a register is directly connected to the rest of the circuit by its input and output wires. In machine-level programming, the registers represent a small collection of addressable words in the CPU, where the addresses consist of register IDs. These words are generally stored in the register file, although we will see that the hardware can sometimes pass a word directly from one instruction to another to avoid the delay of first writing and then reading the register file. When necessary to avoid ambiguity, we will call the two classes of registers "hardware registers" and "program registers," respectively.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623094655694.png" alt="image-20220623094655694" style="zoom:50%;" />

Figure 4.16 gives a more detailed view of a hardware register and how it operates. For most of the time, the register remains in a fixed state (shown as x), generating an output equal to its current state. Signals propagate through the combinational logic preceding the register, creating a new value for the register input (shown as y), but the register output remains fixed as long as the clock is low. As the clock rises, the input signals are loaded into the register as its next state (y), and this becomes the new register output until the next rising clock edge. 

A key point is that the registers serve as barriers between the combinational logic in different parts of the circuit. Values only propagate from a register input to its output once every clock cycle at the rising clock edge. Our Y86-64 processors will use clocked registers to hold the program counter (`PC`), the condition codes (`CC`), and the program status (`Stat`).

The following diagram shows a typical register file:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623095037630.png" alt="image-20220623095037630" style="zoom:50%;" />

This register file has two read ports, named A and B, and one write port, named W. Such a multiported random access memory allows multiple read and write operations to take place simultaneously. In the register file diagrammed, the circuit can read the values of two program registers and update the state of a third. Each port has an address input, indicating which program register should be selected, and a data output or input giving a value for that program register. The addresses are register identifiers, using the encoding shown in Figure 4.4. The two read ports have address inputs `srcA` and `srcB` (short for "source A" and "source B") and data outputs `valA` and `valB` (short for "value A" and "value B"). The write port has address input `dstW` (short for "destination W") and data input `valW` (short for "value W").

The register file is not a combinational circuit, since it has internal storage. In our implementation, however, data can be read from the register file as if it were a block of combinational logic having addresses as inputs and the data as outputs. When either `srcA` or `srcB` is set to some register ID, then, after some delay, the value stored in the corresponding program register will appear on either `valA` or `valB`. For example, setting `srcA` to `3` will cause the value of program register `%rbx` to be read, and this value will appear on output `valA`.

The writing of words to the register file is controlled by the clock signal in a manner similar to the loading of values into a clocked register. Every time the clock rises, the value on input `valW` is written to the program register indicated by the register ID on input `dstW`. When `dstW` is set to the special ID value `0xF`, no program register is written. Since the register file can be both read and written, a natural question to ask is, "What happens if the circuit attempts to read and write the same register simultaneously?" The answer is straightforward: if the same register ID is used for both a read port and the write port, then, as the clock rises, there will be a transition on the read port’s data output from the old value to the new. When we incorporate the register file into our processor design, we will make sure that we take this property into consideration.

Our processor has a random access memory for storing program data, as illustrated below:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623095740391.png" alt="image-20220623095740391" style="zoom:50%;" />

This memory has a single address input, a data input for writing, and a data output for reading. Like the register file, reading from our memory operates in a manner similar to combinational logic: If we provide an address on the address input and set the write control signal to 0, then after some delay, the value stored at that address will appear on data out. The error signal will be set to 1 if the address is out of range, and to 0 otherwise. Writing to the memory is controlled by the clock: We set address to the desired address, data in to the desired value, and write to 1. When we then operate the clock, the specified location in the memory will be updated, as long as the address is valid. As with the read operation, the error signal will be set to 1 if the address is invalid. This signal is generated by combinational logic, since the required bounds checking is purely a function of the address input and does not involve saving any state.

Our processor includes an additional read-only memory for reading instructions. In most actual systems, these memories are merged into a single memory with two ports: one for reading instructions, and the other for reading or writing data.

## 4.3 Sequential Y86-64 Implementations

Now we have the components required to implement a Y86-64 processor. As a first step, we describe a processor called `SEQ` (for "sequential" processor). On each clock cycle, `SEQ` performs all the steps required to process a complete instruction. This would require a very long cycle time, however, and so the clock rate would be unacceptably low. Our purpose in developing `SEQ` is to provide a first step toward our ultimate goal of implementing an efficient pipelined processor.

### 4.3.1 Organizing Processing into Stages

In general, processing an instruction involves a number of operations. We organize them in a particular sequence of stages, attempting to make all instructions follow a uniform sequence, even though the instructions differ greatly in their actions. The detailed processing at each step depends on the particular instruction being executed. Creating this framework will allow us to design a processor that makes best use of the hardware. The following is an informal description of the stages and the operations performed within them:

- ***Fetch.*** The fetch stage reads the bytes of an instruction from memory, using the program counter (PC) as the memory address. From the instruction it extracts the two 4-bit portions of the instruction specifier byte, referred to as `icode` (the instruction code) and `ifun` (the instruction function). It possibly fetches a register specifier byte, giving one or both of the register operand specifiers `rA` and `rB`. It also possibly fetches an 8-byte constant word `valC`. It computes `valP` to be the address of the instruction following the current one in sequential order. That is, `valP` equals the value of the `PC` plus the length of the fetched instruction.
- ***Decode.*** The decode stage reads up to two operands from the register file, giving values `valA` and/or `valB`. Typically, it reads the registers designated by instruction fields `rA` and `rB`, but for some instructions it reads register `%rsp`.
- ***Execute.*** In the execute stage, the arithmetic/logic unit (ALU) either performs the operation specified by the instruction (according to the value of `ifun`), computes the effective address of a memory reference, or increments or decrements the stack pointer. We refer to the resulting value as `valE`. The condition codes are possibly set. For a conditional move instruction, the stage will evaluate the condition codes and move condition (given by `ifun`) and enable the updating of the destination register only if the condition holds. Similarly, for a jump instruction, it determines whether or not the branch should be taken.
- ***Memory.*** The memory stage may write data to memory, or it may read data from memory. We refer to the value read as `valM`.
- ***Write back.*** The write-back stage writes up to two results to the register file.
- ***PC update.*** The `PC` is set to the address of the next instruction.

The processor loops indefinitely, performing these stages. In our simplified implementation, the processor will stop when any exception occurs—that is, when it executes a halt or invalid instruction, or it attempts to read or write an invalid address. In a more complete design, the processor would enter an exception-handling mode and begin executing special code determined by the type of exception.

```antd
const { Popover } = antd
const content = `执行一条指令所需的处理量令人吃惊。`
const Comment = () => {
  return (
  	<p>
    	As can be seen by the preceding description, <Popover content={content}>
<span className="comments">there is a surprising amount of processing required to execute a single instruction.</span></Popover> Not only must we perform the stated operation of the instruction, we must also compute addresses, update stack pointers, and determine the next instruction address. Fortunately, the overall flow can be similar for every instruction. Using a very simple and uniform structure is important when designing hardware, since we want to minimize the total amount of hardware and we must ultimately map it onto the two-dimensional surface of an integrated-circuit chip. One way to minimize the complexity is to have the different instructions share as much of the hardware as possible. For example, each of our processor designs contains a single arithmetic/logic unit that is used in different ways depending on the type of instruction being executed. The cost of duplicating blocks of logic in hardware is much higher than the cost of having multiple copies of code in software. It is also more difficult to deal with many special cases and idiosyncrasies in a hardware system than with software.
  	</p>
  )
}

const root = ReactDOM.createRoot(el)
root.render(<Comment />)
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623102241712.png" alt="image-20220623102241712" style="zoom:50%;" />

Our challenge is to arrange the computing required for each of the different instructions to fit within this general framework. We will use the code shown in Figure 4.17 to illustrate the processing of different Y86-64 instructions. Figures 4.18 through 4.21 contain tables describing how the different Y86-64 instructions proceed through the stages. It is worth the effort to study these tables carefully. They are in a form that enables a straightforward mapping into the hardware. Each line in these tables describes an assignment to some signal or stored state (indicated by the assignment operation ‘←’). These should be read as if they were evaluated in sequence from top to bottom. When we later map the computations to hardware, we will find that we do not need to perform these evaluations in strict sequential order.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623102501966.png" alt="image-20220623102501966" style="zoom:50%;" />

Figure 4.18 shows the processing required for instruction types `OPq` (integer and logical operations), `rrmovq` (register-register move), and `irmovq` (immediateregister move). Let us first consider the integer operations. Examining Figure 4.2, we can see that we have carefully chosen an encoding of instructions so that the four integer operations (`addq`, `subq`, `andq`, and `xorq`) all have the same value of `icode`. We can handle them all by an identical sequence of steps, except that the ALU computation must be set according to the particular instruction operation, encoded in `ifun`.

The processing of an integer-operation instruction follows the general pattern listed above. In the fetch stage, we do not require a constant word, and so `valP` is computed as `PC + 2`. During the decode stage, we read both operands. These are supplied to the ALU in the execute stage, along with the function specifier `ifun`, so that `valE` becomes the instruction result. This computation is shown as the expression `valB OP valA`, where `OP` indicates the operation specified by `ifun`. Note the ordering of the two arguments—this order is consistent with the conventions of Y86-64 (and x86-64). For example, the instruction `subq %rax,%rdx` is supposed to compute the value `R[%rdx] − R[%rax]`. Nothing happens in the memory stage for these instructions, but `valE` is written to register `rB` in the write-back stage, and the PC is set to `valP` to complete the instruction execution.

Executing an `rrmovq` instruction proceeds much like an arithmetic operation. We do not need to fetch the second register operand, however. Instead, we set the second ALU input to zero and add this to the first, giving `valE = valA`, which is then written to the register file. Similar processing occurs for `irmovq`, except that we use constant value `valC` for the first ALU input. In addition, we must increment the program counter by 10 for `irmovq` due to the long instruction format. Neither of these instructions changes the condition codes.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623105821815.png" alt="image-20220623105821815" style="zoom:50%;" />

Figure 4.19 shows the processing required for the memory write and read instructions `rmmovq` and `mrmovq`. We see the same basic flow as before, but using the ALU to add `valC` to `valB`, giving the effective address (the sum of the displacement and the base register value) for the memory operation. In the memory stage, we either write the register value `valA` to memory or read `valM` from memory.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623110137986.png" alt="image-20220623110137986" style="zoom:50%;" />

Figure 4.20 shows the steps required to process `pushq` and `popq` instructions. These are among the most difficult Y86-64 instructions to implement, because they involve both accessing memory and incrementing or decrementing the stack pointer. Although the two instructions have similar flows, they have important differences.

The `pushq` instruction starts much like our previous instructions, but in the decode stage we use `%rsp` as the identifier for the second register operand, giving the stack pointer as value `valB`. In the execute stage, we use the ALU to decrement the stack pointer by 8. This decremented value is used for the memory write address and is also stored back to `%rsp` in the write-back stage. By using `valE` as the address for the write operation, we adhere to the Y86-64 (and x86-64) convention that `pushq` should decrement the stack pointer before writing, even though the actual updating of the stack pointer does not occur until after the memory operation has completed.

The `popq` instruction proceeds much like `pushq`, except that we read two copies of the stack pointer in the decode stage. ***This is clearly redundant, but we will see that having the stack pointer as both `valA` and `valB` makes the subsequent flow more similar to that of other instructions, enhancing the overall uniformity of the design.*** We use the ALU to increment the stack pointer by 8 in the execute stage, but use the unincremented value as the address for the memory operation. In the write-back stage, we update both the stack pointer register with the incremented stack pointer and register `rA` with the value read from memory. Using the unincremented stack pointer as the memory read address preserves the Y86-64 (and x86-64) convention that `popq` should first read memory and then increment the stack pointer.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623121302868.png" alt="image-20220623121302868" style="zoom:50%;" />

Figure 4.21 indicates the processing of our three control transfer instructions: the different `jumps`, `call`, and `ret`. We see that we can implement these instructions with the same overall flow as the preceding ones.

As with integer operations, we can process all of the jumps in a uniform manner, since they differ only when determining whether or not to take the branch. A jump instruction proceeds through fetch and decode much like the previous instructions, except that it does not require a register specifier byte. In the execute stage, we check the condition codes and the jump condition to determine whether or not to take the branch, yielding a 1-bit signal `Cnd`. During the `PC` update stage, we test this flag and set the `PC` to `valC` (the jump target) if the flag is `1` and to `valP` (the address of the following instruction) if the flag is `0`. Our notation `x ? a : b` is similar to the conditional expression in C—it yields a when x is 1 and b when `x` is `0`.

Instructions `call` and `ret` bear some similarity to instructions `pushq` and `popq`, except that we push and pop program counter values. With instruction call, we push `valP`, the address of the instruction that follows the `call` instruction. During the `PC` update stage, we set the `PC` to `valC`, the call destination. With instruction `ret`, we assign `valM`, the value popped from the stack, to the `PC` in the `PC` update stage.

We have created a uniform framework that handles all of the different types of Y86-64 instructions. Even though the instructions have widely varying behavior, we can organize the processing into six stages. Our task now is to create a hardware design that implements the stages and connects them together.

### 4.3.2 SEQ Hardware Structure

The computations required to implement all of the Y86-64 instructions can be organized as a series of six basic stages: fetch, decode, execute, memory, write back, and PC update. Figure 4.22 shows an abstract view of a hardware structure that can perform these computations. The program counter is stored in a register, shown in the lower left-hand corner (labeled "PC"). Information then flows along wires (shown grouped together as a heavy gray line), first upward and then around to the right. Processing is performed by hardware units associated with the different stages. The feedback paths coming back down on the right-hand side contain the updated values to write to the register file and the updated program counter. In SEQ, all of the processing by the hardware units occurs within a single clock cycle, as is discussed in Section 4.3.3. This diagram omits some small blocks of combinational logic as well as all of the control logic needed to operate the different hardware units and to route the appropriate values to the units. We will add this detail later. Our method of drawing processors with the flow going from bottom to top is unconventional. We will explain the reason for this convention when we start designing pipelined processors.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220623145108.png" alt="QQ截图20220623145108" style="zoom:50%;" />

The hardware units are associated with the different processing stages:

- **Fetch.** Using the program counter register as an address, the instruction memory reads the bytes of an instruction. The PC incrementer computes `valP`, the incremented program counter.

- **Decode.** The register file has two read ports, A and B, via which register values `valA` and `valB` are read simultaneously.

- **Execute.** The execute stage uses the arithmetic/logic (ALU) unit for different purposes according to the instruction type. For integer operations, it performs the specified operation. For other instructions, it serves as an adder to compute an incremented or decremented stack pointer, to compute an effective address, or simply to pass one of its inputs to its outputs by adding zero.

  The condition code register (CC) holds the three condition code bits. New values for the condition codes are computed by the ALU. When executing a conditional move instruction, the decision as to whether or not to update the destination register is computed based on the condition codes and move condition. Similarly, when executing a jump instruction, the branch signal `Cnd` is computed based on the condition codes and the jump type.

- **Memory.** The data memory reads or writes a word of memory when executing a memory instruction. The instruction and data memories access the same memory locations, but for different purposes.

- **Write back.** The register file has two write ports. `Port E` is used to write values computed by the ALU, while `port M` is used to write values read from the data memory.

- **PC update.** The new value of the program counter is selected to be either `valP`, the address of the next instruction, `valC`, the destination address specified by a `call` or `jump` instruction, or `valM`, the return address read from memory.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220623150112.png" alt="QQ截图20220623150112" style="zoom:50%;" />

Figure 4.23 gives a more detailed view of the hardware required to implement `SEQ` (although we will not see the complete details until we examine the individual stages). We see the same set of hardware units as earlier, but now the wires are shown explicitly. In this figure, as well as in our other hardware diagrams, we use the following drawing conventions:

- Clocked registers are shown as white rectangles.The program counter `PC` is the only clocked register in `SEQ`.
- Hardware units are shown as light blue boxes. These include the memories, the ALU, and so forth. We will use the same basic set of units for all of our processor implementations. We will treat these units as "black boxes" and not go into their detailed designs.
- Control logic blocks are drawn as gray rounded rectangles.These blocks serve to select from among a set of signal sources or to compute some Boolean function. We will examine these blocks in complete detail, including developing HCL descriptions.
- Wire names are indicated in white circles.These are simply labels on the wires, not any kind of hardware element.
- Word-wide data connections are shown as medium lines. Each of these lines actually represents a bundle of 64 wires, connected in parallel, for transferring a word from one part of the hardware to another.
- Byte and narrower data connections are shown as thin lines.Each of these lines actually represents a bundle of four or eight wires, depending on what type of values must be carried on the wires.
- Single-bit connections are shown as dotted lines.These represent control values passed between the units and blocks on the chip.

All of the computations we have shown in Figures 4.18 through 4.21 have the property that each line represents either the computation of a specific value, such as `valP`, or the activation of some hardware unit, such as the memory. These computations and actions are listed in the second column of Figure 4.24. In addition to the signals we have already described, this list includes four register ID signals: `srcA`, the source of `valA`; `srcB`, the source of `valB`; `dstE`, the register to which `valE` gets written; and `dstM`, the register to which `valM` gets written.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220623151747148.png" alt="image-20220623151747148" style="zoom:50%;" />

The two right-hand columns of this figure show the computations for the `OPq` and `mrmovq` instructions to illustrate the values being computed. To map the computations into hardware, we want to implement control logic that will transfer the data between the different hardware units and operate these units in such a way that the specified operations are performed for each of the different instruction types. That is the purpose of the control logic blocks, shown as gray rounded boxes in Figure 4.23. Our task is to proceed through the individual stages and create detailed designs for these blocks.

### 4.3.3 SEQ Timing

In introducing the tables of Figures 4.18 through 4.21, we stated that they should be read as if they were written in a programming notation, with the assignments performed in sequence from top to bottom. On the other hand, the hardware structure of Figure 4.23 operates in a fundamentally different way, with a single clock transition triggering a flow through combinational logic to execute an entire instruction. Let us see how the hardware can implement the behavior listed in these tables.

Our implementation of `SEQ` consists of combinational logic and two forms of memory devices: clocked registers (the program counter and condition code register) and random access memories (the register file, the instruction memory, and the data memory). Combinational logic does not require any sequencing or control—values propagate through a network of logic gates whenever the inputs change. As we have described, we also assume that reading from a random access memory operates much like combinational logic, with the output word generated based on the address input. This is a reasonable assumption for smaller memories (such as the register file), and we can mimic this effect for larger circuits using special clock circuits. Since our instruction memory is only used to read instructions, we can therefore treat this unit as if it were combinational logic.

 We are left with just four hardware units that require an explicit control over their sequencing—the program counter, the condition code register, the data memory, and the register file. These are controlled via a single clock signal that triggers the loading of new values into the registers and the writing of values to the random access memories. The program counter is loaded with a new instruction address every clock cycle. The condition code register is loaded only when an integer operation instruction is executed. The data memory is written only when an `rmmovq`, `pushq`, or `call` instruction is executed. The two write ports of the register file allow two program registers to be updated on every cycle, but we can use the special register ID `0xF` as a port address to indicate that no write should be performed for this port.

This clocking of the registers and memories is all that is required to control the sequencing of activities in our processor. Our hardware achieves the same effect as would a sequential execution of the assignments shown in the tables of Figures 4.18 through 4.21, even though all of the state updates actually occur simultaneously and only as the clock rises to start the next cycle. This equivalence holds because of the nature of the Y86-64 instruction set, and because we have organized the computations in such a way that our design obeys the following principle:

```info
PRINCIPLE: No reading back

The processor never needs to read back the state updated by an instruction in order to complete the processing of this instruction.
```

This principle is crucial to the success of our implementation. As an illustration, suppose we implemented the `pushq` instruction by first decrementing `%rsp` by 8 and then using the updated value of `%rsp` as the address of a write operation. This approach would violate the principle stated above. It would require reading the updated stack pointer from the register file in order to perform the memory operation. Instead, our implementation (Figure 4.20) generates the decremented value of the stack pointer as the signal `valE` and then uses this signal both as the data for the register write and the address for the memory write. As a result, it can perform the register and memory writes simultaneously as the clock rises to begin the next clock cycle.

As another illustration of this principle, we can see that some instructions (the integer operations) set the condition codes, and some instructions (the conditional move and jump instructions) read these condition codes, but no instruction must both set and then read the condition codes. Even though the condition codes are not set until the clock rises to begin the next clock cycle, they will be updated before any instruction attempts to read them.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220623232338.png" alt="QQ截图20220623232338" style="zoom:50%;" />

Figure 4.25 shows how the SEQ hardware would process the instructions at lines 3 and 4 in the following code sequence, shown in assembly code with the instruction addresses listed on the left:

```C
0x000: irmovq $0x100,%rbx # %rbx <-- 0x100
0x00a: irmovq $0x200,%rdx # %rdx <-- 0x200
0x014: addq %rdx,%rbx # %rbx <-- 0x300 CC <-- 000
0x016: je dest # Not taken
0x01f: rmmovq %rbx,0(%rdx) # M[0x200] <-- 0x300
0x029: dest: halt
```

Each of the diagrams labeled 1 through 4 shows the four state elements plus the combinational logic and the connections among the state elements. We show the combinational logic as being wrapped around the condition code register, because some of the combinational logic (such as the ALU) generates the input to the condition code register, while other parts (such as the branch computation and the PC selection logic) have the condition code register as input. We show the register file and the data memory as having separate connections for reading and writing, since the read operations propagate through these units as if they were combinational logic, while the write operations are controlled by the clock.

The color coding in Figure 4.25 indicates how the circuit signals relate to the different instructions being executed. We assume the processing starts with the condition codes, listed in the order ZF, SF, and OF, set to 100. At the beginning of clock cycle 3 (point 1), the state elements hold the state as updated by the second `irmovq` instruction (line 2 of the listing), shown in light gray. The combinational logic is shown in white, indicating that it has not yet had time to react to the changed state. The clock cycle begins with address `0x014` loaded into the program counter. This causes the `addq` instruction (line 3 of the listing), shown in blue, to be fetched and processed. Values flow through the combinational logic, including the reading of the random access memories. By the end of the cycle (point 2), the combinational logic has generated new values (000) for the condition codes, an update for program register `%rbx`, and a new value (`0x016`) for the program counter. At this point, the combinational logic has been updated according to the `addq` instruction (shown in blue), but the state still holds the values set by the second `irmovq` instruction (shown in light gray).

As the clock rises to begin cycle 4 (point 3), the updates to the program counter, the register file, and the condition code register occur, and so we show these in blue, but the combinational logic has not yet reacted to these changes, and so we show this in white. In this cycle, the `je` instruction (line 4 in the listing), shown in dark gray, is fetched and executed. Since condition code `ZF` is 0, the branch is not taken. By the end of the cycle (point 4), a new value of `0x01f` has been generated for the program counter. The combinational logic has been updated according to the `je` instruction (shown in dark gray), but the state still holds the values set by the `addq` instruction (shown in blue) until the next cycle begins.

As this example illustrates, the use of a clock to control the updating of the state elements, combined with the propagation of values through combinational logic, suffices to control the computations performed for each instruction in our implementation of SEQ. Every time the clock transitions from low to high, the processor begins executing a new instruction.

### 4.3.4 SEQ Stage Implementations

In this section, we devise HCL descriptions for the control logic blocks required to implement `SEQ`.  We show some example blocks here, and others are given as practice problems. We recommend that you work these problems as a way to check your understanding of how the blocks relate to the computational requirements of the different instructions.

Part of the HCL description of `SEQ` that we do not include here is a definition of the different integer and Boolean signals that can be used as arguments to the HCL operations. These include the names of the different hardware signals, as well as constant values for the different instruction codes, function codes, register names, ALU operations, and status codes. Only those that must be explicitly referenced in the control logic are shown. The constants we use are documented in Figure 4.26. By convention, we use uppercase names for constant values.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220624163149362.png" alt="image-20220624163149362" style="zoom:50%;" />

In addition to the instructions shown in Figures 4.18 to 4.21, we include the processing for the `nop` and `halt` instructions. The `nop` instruction simply flows through stages without much processing, except to increment the `PC` by `1`. The `halt` instruction causes the processor status to be set to `HLT`, causing it to halt operation.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220624194301496.png"  style="zoom:50%;" />

#### Fetch Stage

```antd
const { Popover } = antd

const Paragraph = () => {
	return (
		<p>
			As shown in <Popover style={{cursor: 'pointer', padding: 0}} content={() => <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220624194301496.png" style={{zoom: '50%'}} />}><span className="comments">Figure 4.27</span></Popover>, the fetch stage includes the instruction memory hardware unit. This unit reads 10 bytes from memory at a time, using the PC as the address of the first byte (byte 0). This byte is interpreted as the instruction byte and is split (by the unit labeled "Split") into two 4-bit quantities. The control logic blocks labeled <code>"icode"</code> and <code>"ifun"</code> then compute the instruction and function codes as equaling either the values read from memory or, in the event that the instruction address is not valid (as indicated by the signal <code>imem_error</code>), the values corresponding to a <code>nop</code> instruction. Based on the value of <code>icode</code>, we can compute three 1-bit signals (shown as dashed lines):
		</p>
	)
}

const root = ReactDOM.createRoot(el)
root.render(<Paragraph />)
```

- `instr_valid`. Does this byte correspond to a legal Y86-64 instruction? This signal is used to detect an illegal instruction. 
- `need_regids`. Does this instruction include a register specifier byte? 
- `need_valC`. Does this instruction include a constant word?

The signals `instr_valid` and `imem_error` (generated when the instruction address is out of bounds) are used to generate the status code in the memory stage.

As an example, the HCL description for `need_regids` simply determines whether the value of `icode` is one of the instructions that has a register specifier byte:

```C
bool need_regids = icode in { 
  IRRMOVQ, IOPQ, IPUSHQ, IPOPQ, IIRMOVQ, IRMMOVQ, IMRMOVQ 
};
```

As Figure 4.27 shows, the remaining 9 bytes read from the instruction memory encode some combination of the register specifier byte and the constant word. These bytes are processed by the hardware unit labeled "Align" into the register fields and the constant word. Byte 1 is split into register specifiers `rA` and `rB` when the computed signal `need_regids` is `1`. If `need_regids` is `0`, both register specifiers are set to `0xF(RNONE)`, indicating there are no registers specified by this instruction. Recall also (Figure 4.2) that for any instruction having only one register operand, the other field of the register specifier byte will be `0xF(RNONE)`. Thus, we can assume that the signals `rA` and `rB` either encode registers we want to access or indicate that register access is not required. The unit labeled "Align" also generates the constant word `valC`. This will either be `bytes 1–8` or `bytes 2–9`, depending on the value of signal `need_regids`.

The `PC` incrementer hardware unit generates the signal `valP`, based on the current value of the `PC`, and the two signals `need_regids` and `need_valC`. For `PC` value `p`, `need_regids` value `r`, and `need_valC` value `i`, the incrementer generates the value `p + 1 + r + 8i`.

#### Decode and Write-Back Stages

Figure 4.28 provides a detailed view of logic that implements both the decode and write-back stages in `SEQ`. These two stages are combined because they both access the register file.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220701214209345.png" alt="image-20220701214209345" style="zoom:50%;" />

The register file has four ports. It supports up to two simultaneous reads (on ports A and B) and two simultaneous writes (on ports E and M). Each port has both an address connection and a data connection, where the address connection is a register ID, and the data connection is a set of 64 wires serving as either an output word (for a read port) or an input word (for a write port) of the register file. The two read ports have address inputs `srcA` and `srcB`, while the two write ports have address inputs `dstE` and `dstM`. The special identifier `0xF` (RNONE) on an address port indicates that no register should be accessed.

The four blocks at the bottom of Figure 4.28 generate the four different register IDs for the register file, based on the instruction code `icode`, the register specifiers `rA` and `rB`, and possibly the condition signal `Cnd` computed in the execute stage. Register ID `srcA` indicates which register should be read to generate `valA`. The desired value depends on the instruction type, as shown in the first row for the decode stage in Figures 4.18 to 4.21. Combining all of these entries into a single computation gives the following `HCL` description of `srcA` (recall that `RESP` is the register ID of `%rsp`):

```C
word srcA = [
  icode in { IRRMOVQ, IRMMOVQ, IOPQ, IPUSHQ } : rA;
  icode in { IPOPQ, IRET } : RRSP;
  1 : RNONE; # Don’t need register
];
```

Register ID `dstE` indicates the destination register for write port E, where the computed value `valE` is stored. This is shown in Figures 4.18 to 4.21 as the first step in the write-back stage. If we ignore for the moment the conditional move instructions, then we can combine the destination registers for all of the different instructions to give the following `HCL` description of `dstE`:

```C
# WARNING: Conditional move not implemented correctly here
word dstE = [
  icode in { IRRMOVQ } : rB;
  icode in { IIRMOVQ, IOPQ} : rB;
  icode in { IPUSHQ, IPOPQ, ICALL, IRET } : RRSP;
  1 : RNONE; # Don’t write any register
];
```

We will revisit this signal and how to implement conditional moves when we examine the execute stage.

#### Execute Stage

The execute stage includes the arithmetic/logic unit (ALU). This unit performs the operation add, subtract, and, or exclusive-or on inputs `aluA` and `aluB` based on the setting of the `alufun` signal. These data and control signals are generated by three control blocks, as diagrammed in Figure 4.29. The ALU output becomes the signal `valE`.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220701215410348.png" alt="image-20220701215410348" style="zoom:50%;" />

In Figures 4.18 to 4.21, the ALU computation for each instruction is shown as the first step in the execute stage. The operands are listed with `aluB` first, followed by `aluA` to make sure the `subq` instruction subtracts `valA` from `valB`. We can see that the value of `aluA` can be `valA`, `valC`, or either `−8` or `+8`, depending on the instruction type. We can therefore express the behavior of the control block that generates aluA as follows:

```C
word aluA = [
  icode in { IRRMOVQ, IOPQ } : valA;
  icode in { IIRMOVQ, IRMMOVQ, IMRMOVQ } : valC;
  icode in { ICALL, IPUSHQ } : -8;
  icode in { IRET, IPOPQ } : 8;
  # Other instructions don’t need ALU
];
```

Looking at the operations performed by the ALU in the execute stage, we can see that it is mostly used as an adder. For the `OPq` instructions, however, we want it to use the operation encoded in the `ifun` field of the instruction. We can therefore write the `HCL` description for the ALU control as follows:

```C
word alufun = [
  icode == IOPQ : ifun;
  1 : ALUADD;
];
```

The execute stage also includes the condition code register. Our ALU generates the three signals on which the condition codes are based—zero, sign, and overflow—every time it operates. However, we only want to set the condition codes when an `OPq` instruction is executed. We therefore generate a signal `set_cc` that controls whether or not the condition code register should be updated:

```C
bool set_cc = icode in { IOPQ };
```

The hardware unit labeled `"cond"` uses a combination of the condition codes and the function code to determine whether a conditional branch or data transfer should take place (Figure 4.3). It generates the `Cnd` signal used both for the setting of `dstE` with conditional moves and in the next `PC` logic for conditional branches. For other instructions, the `Cnd` signal may be set to either `1` or `0`, depending on the instruction’s function code and the setting of the condition codes, but it will be ignored by the control logic. We omit the detailed design of this unit.

#### Memory Stage

The memory stage has the task of either reading or writing program data. As shown in Figure 4.30, two control blocks generate the values for the memory address and the memory input data (for write operations). Two other blocks generate the control signals indicating whether to perform a read or a write operation. When a read operation is performed, the data memory generates the value `valM`.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220701221126410.png" alt="image-20220701221126410" style="zoom:50%;" />

The desired memory operation for each instruction type is shown in the memory stage of Figures 4.18 to 4.21. Observe that the address for memory reads and writes is always `valE` or `valA`. We can describe this block in `HCL` as follows:

```C
word mem_addr = [
  icode in { IRMMOVQ, IPUSHQ, ICALL, IMRMOVQ } : valE;
  icode in { IPOPQ, IRET } : valA;
  # Other instructions don’t need address
];
```

We want to set the control signal `mem_read` only for instructions that read data from memory, as expressed by the following HCL code:

```C
bool mem_read = icode in { IMRMOVQ, IPOPQ, IRET };
```

A final function for the memory stage is to compute the status code `Stat` resulting from the instruction execution according to the values of `icode`, `imem_error`, and `instr_valid` generated in the fetch stage and the signal `dmem_error` generated by the data memory.

#### PC Update Stage

The final stage in `SEQ` generates the new value of the program counter (see Figure 4.31). 

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220702075930286.png" alt="image-20220702075930286" style="zoom:50%;" />

As the final steps in Figures 4.18 to 4.21 show, the new `PC` will be `valC`, `valM`, or `valP`, depending on the instruction type and whether or not a branch should be taken. This selection can be described in `HCL` as follows:

```C
word new_pc = [
  # Call. Use instruction constant
  icode == ICALL : valC;
  # Taken branch. Use instruction constant
  icode == IJXX && Cnd : valC;
  # Completion of RET instruction. Use value from stack
  icode == IRET : valM;
  # Default: Use incremented PC
  1 : valP;
];
```

#### Surveying SEQ

We have now stepped through a complete design for a Y86-64 processor. We have seen that by organizing the steps required to execute each of the different instructions into a uniform flow, we can implement the entire processor with a small number of different hardware units and with a single clock to control the sequencing of computations. The control logic must then route the signals between these units and generate the proper control signals based on the instruction types and the branch conditions.

The only problem with `SEQ` is that it is too slow. The clock must run slowly enough so that signals can propagate through all of the stages within a single cycle. As an example, consider the processing of a `ret` instruction. Starting with an updated program counter at the beginning of the clock cycle, the instruction must be read from the instruction memory, the stack pointer must be read from the register file, the `ALU` must increment the stack pointer by 8, and the return address must be read from the memory in order to determine the next value for the program counter. All of these must be completed by the end of the clock cycle.

This style of implementation does not make very good use of our hardware units, since each unit is only active for a fraction of the total clock cycle. We will see that we can achieve much better performance by introducing pipelining.

## 4.4 General Principles of Pipelining

Before attempting to design a pipelined Y86-64 processor, let us consider some general properties and principles of pipelined systems. Such systems are familiar to anyone who has been through the serving line at a cafeteria or run a car through an automated car wash. In a pipelined system, the task to be performed is divided into a series of discrete stages. In a cafeteria, this involves supplying salad, a main dish, dessert, and beverage. In a car wash, this involves spraying water and soap, scrubbing, applying wax, and drying. Rather than having one customer run through the entire sequence from beginning to end before the next can begin, we allow multiple customers to proceed through the system at once. In a traditional cafeteria line, the customers maintain the same order in the pipeline and pass through all stages, even if they do not want some of the courses. In the case of the car wash, a new car is allowed to enter the spraying stage as the preceding car moves from the spraying stage to the scrubbing stage. <span class="comments">In general, the cars must move through the system at the same rate to avoid having one car crash into the next.</span>

A key feature of pipelining is that it increases the throughput of the system (i.e., the number of customers served per unit time), but it may also slightly increase the latency (i.e., the time required to service an individual customer). For example, a customer in a cafeteria who only wants a dessert could pass through a nonpipelined system very quickly, stopping only at the dessert stage. A customer in a pipelined system who attempts to go directly to the dessert stage risks incurring the wrath of other customers.

### 4.4.1 Computational Pipelines

Shifting our focus to computational pipelines, the "customers" are instructions and the stages perform some portion of the instruction execution. Figure 4.32(a) shows an example of a simple nonpipelined hardware system. It consists of some logic that performs a computation, followed by a register to hold the results of this computation. A clock signal controls the loading of the register at some regular time interval. An example of such a system is the decoder in a compact disk (CD) player. The incoming signals are the bits read from the surface of the CD, and the logic decodes these to generate audio signals. The computational block in the figure is implemented as combinational logic, meaning that the signals will pass through a series of logic gates, with the outputs becoming some function of the inputs after some time delay.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220702085643656.png" alt="image-20220702085643656" style="zoom:50%;" />

In contemporary logic design, we measure circuit delays in units of picoseconds (abbreviated "ps"), or $10^{-12}$ seconds. In this example, we assume the combinational logic requires 300 ps, while the loading of the register requires 20 ps. Figure 4.32 shows a form of timing diagram known as a pipeline diagram. In this diagram, time flows from left to right. A series of instructions (here named `I1`, `I2`, and `I3`) are written from top to bottom. The solid rectangles indicate the times during which these instructions are executed. In this implementation, we must complete one instruction before beginning the next. Hence, the boxes do not overlap one another vertically. The following formula gives the maximum rate at which we could operate the system:
$$
\text{Throughput} = \frac{1\,\,\, \text{instruction}}{(20 + 300) \,\, \text{picoseconds}} \cdot \frac{1,000 \,\, \text{picoseconds}}{1 \,\,\text{nanosecond}} \approx 3.12 \,\, \text{GIPS}
$$
We express throughput in units of giga-instructions per second (abbreviated GIPS), or billions of instructions per second. The total time required to perform a single instruction from beginning to end is known as the latency. In this system, the latency is 320 ps, the reciprocal of the throughput.

Suppose we could divide the computation performed by our system into three stages, A, B, and C, where each requires 100 ps, as illustrated in Figure 4.33. Then we could put pipeline registers between the stages so that each instruction moves through the system in three steps, requiring three complete clock cycles from beginning to end. As the pipeline diagram in Figure 4.33 illustrates, we could allow `I2` to enter stage A as soon as `I1` moves from A to B, and so on. In steady state, all three stages would be active, with one instruction leaving and a new one entering the system every clock cycle. We can see this during the third clock cycle in the pipeline diagram where `I1` is in stage C, `I2` is in stage B, and `I3` is in stage A. In this system, we could cycle the clocks every $100 + 20 = 120$ picoseconds, giving a throughput of around $8.33$ GIPS. Since processing a single instruction requires 3 clock cycles, the latency of this pipeline is $3 \times 120 = 360 \text{ps}$. We have increased the throughput of the system by a factor of $8.33/3.12 = 2.67$ at the expense of some added hardware and a slight increase in the latency ($360/320 = 1.12$). The increased latency is due to the time overhead of the added pipeline registers.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220702090905257.png" alt="image-20220702090905257" style="zoom:50%;" />

### 4.4.2 A Detailed Look at Pipeline Operation

To better understand how pipelining works, let us look in some detail at the timing and operation of pipeline computations. Figure 4.34 shows the pipeline diagram for the three-stage pipeline we have already looked at (Figure 4.33). The transfer of the instructions between pipeline stages is controlled by a clock signal, as shown above the pipeline diagram. Every 120 ps, this signal rises from 0 to 1, initiating the next set of pipeline stage evaluations.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220702091359211.png" alt="image-20220702091359211" style="zoom:50%;" />

Figure 4.35 traces the circuit activity between times 240 and 360, as instruction `I1` (shown in dark gray) propagates through stage C, `I2` (shown in blue) propagates through stage B, and `I3` (shown in light gray) propagates through stage A. Just before the rising clock at time `240` (point 1), the values computed in stage A for instruction `I2` have reached the input of the first pipeline register, but its state and output remain set to those computed during stage A for instruction `I1`. The values computed in stage B for instruction `I1` have reached the input of the second pipeline register. As the clock rises, these inputs are loaded into the pipeline registers, becoming the register outputs (point `2`). In addition, the input to stage A is set to initiate the computation of instruction `I3`. The signals then propagate through the combinational logic for the different stages (point 3). As the curved wave fronts in the diagram at point `3` suggest, signals can propagate through different sections at different rates. Before time `360`, the result values reach the inputs of the pipeline registers (point 4). When the clock rises at time `360`, each of the instructions will have progressed through one pipeline stage.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220702134302.png" alt="QQ截图20220702134302" style="zoom:50%;" />

We can see from this detailed view of pipeline operation that slowing down the clock would not change the pipeline behavior. The signals propagate to the pipeline register inputs, but no change in the register states will occur until the clock rises. On the other hand, we could have disastrous effects if the clock were run too fast. The values would not have time to propagate through the combinational logic, and so the register inputs would not yet be valid when the clock rises.

```antd
const { Popover } = antd

const Paragraph = () => {
	return (
		<p>
			As with our discussion of the timing for the SEQ processor (Section 4.3.3), <Popover content={'我们看到，在组合逻辑块之间有时钟寄存器的简单机制就足以控制流水线中的指令流。'}><span className='comments'>we see that the simple mechanism of having clocked registers between blocks of combinational logic suffices to control the flow of instructions in the pipeline.</span></Popover> As the clock rises and falls repeatedly, the different instructions flow through the stages of the pipeline without interfering with one another.
		</p>
	)
}

const root = ReactDOM.createRoot(el)
root.render(<Paragraph />)
```

### 4.4.3 Limitations of Pipelining
The example of Figure 4.33 shows an ideal pipelined system in which we are able to divide the computation into three independent stages, each requiring one-third of the time required by the original logic. Unfortunately, other factors often arise that diminish the effectiveness of pipelining.

#### Nonuniform Partitioning

Figure 4.36 shows a system in which we divide the computation into three stages as before, but the delays through the stages range from 50 to 150 ps. The sum of the delays through all of the stages remains 300 ps. However, the rate at which we can operate the clock is limited by the delay of the slowest stage. As the pipeline diagram in this figure shows, stage A will be idle (shown as a white box) for 100 ps every clock cycle, while stage C will be idle for 50 ps every clock cycle. Only stage B will be continuously active. We must set the clock cycle to $150 + 20 = 170$ picoseconds, giving a throughput of 5.88 GIPS. In addition, the latency would increase to 510 ps due to the slower clock rate.

![image-20220703111638960](https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703111638960.png)

Devising a partitioning of the system computation into a series of stages having uniform delays can be a major challenge for hardware designers. Often, some of the hardware units in a processor, such as the ALU and the memories, cannot be subdivided into multiple units with shorter delay. This makes it difficult to create a set of balanced stages. We will not concern ourselves with this level of detail in designing our pipelined Y86-64 processor, but it is important to appreciate the importance of timing optimization in actual system design.

#### Diminishing Returns of Deep Pipelining

Figure 4.37 illustrates another limitation of pipelining. In this example, we have divided the computation into six stages, each requiring 50 ps. Inserting a pipeline register between each pair of stages yields a six-stage pipeline. The minimum clock period for this system is $50 + 20 = 70$ picoseconds, giving a throughput of 14.29 GIPS. Thus, in doubling the number of pipeline stages, we improve the performance by a factor of $14.29/8.33 = 1.71$. Even though we have cut the time required for each computation block by a factor of 2, we do not get a doubling of the throughput, **due to the delay through the pipeline registers**. This delay becomes a limiting factor in the throughput of the pipeline. In our new design, this delay consumes $28.6\%$ of the total clock period.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703112059626.png" alt="image-20220703112059626" style="zoom:50%;" />

Modern processors employ very deep pipelines (15 or more stages) in an attempt to maximize the processor clock rate. The processor architects divide the instruction execution into a large number of very simple steps so that each stage can have a very small delay. The circuit designers carefully design the pipeline registers to minimize their delay. The chip designers must also carefully design the clock distribution network to ensure that the clock changes at the exact same time across the entire chip. All of these factors contribute to the challenge of designing high-speed microprocessors.

### 4.4.4 Pipelining a System with Feedback
Up to this point, we have considered only systems in which the objects passing through the pipeline—whether cars, people, or instructions—are completely independent of one another. For a system that executes machine programs such as x86-64 or Y86-64, however, there are potential dependencies between successive instructions. For example, consider the following Y86-64 instruction sequence:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703112458058.png" alt="image-20220703112458058" style="zoom:50%;" />

In this three-instruction sequence, there is a data dependency between each successive pair of instructions, as indicated by the circled register names and the arrows between them. The `irmovq` instruction (line 1) stores its result in `%rax`, which then must be read by the `addq` instruction (line 2); and this instruction stores its result in `%rbx`, which must then be read by the `mrmovq` instruction (line 3).

Another source of sequential dependencies occurs due to the instruction control flow. Consider the following Y86-64 instruction sequence:

```assembly
loop:
	subq %rdx,%rbx
	jne targ
	irmovq $10,%rdx
	jmp loop
targ:
	halt
```

The `jne` instruction (line 3) creates a control dependency since the outcome of the conditional test determines whether the next instruction to execute will be the `irmovq` instruction (line 4) or the `halt` instruction (line 7). In our design for SEQ, these dependencies were handled by the feedback paths shown on the right-hand side of Figure 4.22. This feedback brings the updated register values down to the register file and the new `PC` value down to the `PC` register.

Figure 4.38 illustrates the perils of introducing pipelining into a system containing feedback paths. In the original system (Figure 4.38(a)), the result of each instruction is fed back around to the next instruction. This is illustrated by the pipeline diagram (Figure 4.38(b)), where the result of `I1` becomes an input to `I2`, and so on. If we attempt to convert this to a three-stage pipeline in the most straightforward manner (Figure 4.38(c)), we change the behavior of the system. As Figure 4.38(c) shows, the result of `I1` becomes an input to `I4`. In attempting to speed up the system via pipelining, we have changed the system behavior.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220703121901.png" alt="QQ截图20220703121901" style="zoom:50%;" />

When we introduce pipelining into a Y86-64 processor, we must deal with feedback effects properly. Clearly, it would be unacceptable to alter the system behavior as occurred in the example of Figure 4.38. Somehow we must deal with the data and control dependencies between instructions so that the resulting behavior matches the model defined by the ISA.

## 4.5 Pipelined Y86-64 Implementations

We are finally ready for the major task of this chapter—designing a pipelined Y86-64 processor. We start by making a small adaptation of the sequential processor `SEQ` to shift the computation of the PC into the fetch stage. We then add pipeline registers between the stages. Our first attempt at this does not handle the different data and control dependencies properly. By making some modifications, however, we achieve our goal of an efficient pipelined processor that implements the Y86-64 ISA.

### 4.5.1 SEQ+: Rearranging the Computation Stages

As a transitional step toward a pipelined design, we must slightly rearrange the order of the five stages in `SEQ` so that the PC update stage comes at the beginning of the clock cycle, rather than at the end. This transformation requires only minimal change to the overall hardware structure, and it will work better with the sequencing of activities within the pipeline stages. We refer to this modified design as `SEQ+`.

We can move the `PC` update stage so that its logic is active at the beginning of the clock cycle by making it compute the `PC` value for the current instruction. Figure 4.39 shows how `SEQ` and `SEQ+` differ in their `PC` computation. With `SEQ` (Figure 4.39(a)), the `PC` computation takes place at the end of the clock cycle, computing the new value for the `PC` register based on the values of signals computed during the current clock cycle. With `SEQ+` (Figure 4.39(b)), we create state registers to hold the signals computed during an instruction. Then, as a new clock cycle begins, the values propagate through the exact same logic to compute the `PC` for the now-current instruction. We label the registers `"pIcode," "pCnd,"` and so on, to indicate that on any given cycle, they hold the control signals generated during the previous cycle.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703143836218.png" alt="image-20220703143836218" style="zoom:50%;" />

Figure 4.40 shows a more detailed view of the SEQ+ hardware. We can see that it contains the exact same hardware units and control blocks that we had in SEQ (Figure 4.23), but with the PC logic shifted from the top, where it was active at the end of the clock cycle, to the bottom, where it is active at the beginning.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220703145522.png" alt="QQ截图20220703145522" style="zoom:50%;" />

The shift of state elements from SEQ to SEQ+ is an example of a general transformation known as circuit retiming. Retiming changes the state representation for a system without changing its logical behavior. It is often used to balance the delays between the different stages of a pipelined system.

### 4.5.2 Inserting Pipeline Registers

In our first attempt at creating a pipelined Y86-64 processor, we insert pipeline registers between the stages of `SEQ+` and rearrange signals somewhat, yielding the `PIPE−` processor, where the `"−"` in the name signifies that this processor has somewhat less performance than our ultimate processor design. The structure of `PIPE−` is illustrated in Figure 4.41. The pipeline registers are shown in this figure as blue boxes, each containing different fields that are shown as white boxes. As indicated by the multiple fields, each pipeline register holds multiple bytes and words. Unlike the labels shown in rounded boxes in the hardware structure of the two sequential processors (Figures 4.23 and 4.40), these white boxes represent actual hardware components.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220703151617.png" alt="QQ截图20220703151617" style="zoom:50%;" />

Observe that `PIPE−` uses nearly the same set of hardware units as our sequential design `SEQ` (Figure 4.40), but with the pipeline registers separating the stages. The differences between the signals in the two systems is discussed in Section 4.5.3.

The pipeline registers are labeled as follows:

- `F` holds a predicted value of the program counter, as will be discussed shortly.
- `D` sits between the fetch and decode stages. It holds information about the most recently fetched instruction for processing by the decode stage.
- `E` sits between the decode and execute stages. It holds information about the most recently decoded instruction and the values read from the register file for processing by the execute stage.
- `M` sits between the execute and memory stages. It holds the results of the most recently executed instruction for processing by the memory stage. It also holds information about branch conditions and branch targets for processing conditional jumps.
- `W` sits between the memory stage and the feedback paths that supply the computed results to the register file for writing and the return address to the `PC` selection logic when completing a `ret` instruction.

Figure 4.42 shows how the following code sequence would flow through our five-stage pipeline, where the comments identify the instructions as `I1` to `I5` for reference:

```assembly
irmovq $1,%rax # I1
irmovq $2,%rbx # I2
irmovq $3,%rcx # I3
irmovq $4,%rdx # I4
halt
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703152649207.png" alt="image-20220703152649207" style="zoom:50%;" />

The right side of the figure shows a pipeline diagram for this instruction sequence. As with the pipeline diagrams for the simple pipelined computation units of Section 4.4, this diagram shows the progression of each instruction through the pipeline stages, with time increasing from left to right. The numbers along the top identify the clock cycles at which the different stages occur. For example, in cycle 1, instruction `I1` is fetched, and it then proceeds through the pipeline stages, with its result being written to the register file after the end of cycle 5. Instruction `I2` is fetched in cycle 2, and its result is written back after the end of cycle 6, and so on. At the bottom, we show an expanded view of the pipeline for cycle 5. At this point, there is an instruction in each of the pipeline stages.

From Figure 4.42, we can also justify our convention of drawing processors so that the instructions flow from bottom to top. The expanded view for cycle 5 shows the pipeline stages with the fetch stage on the bottom and the write-back stage on the top, just as do our diagrams of the pipeline hardware (Figure 4.41). If we look at the ordering of instructions in the pipeline stages, we see that they appear in the same order as they do in the program listing. Since normal program flow goes from top to bottom of a listing, we preserve this ordering by having the pipeline flow go from bottom to top. This convention is particularly useful when working with the simulators that accompany this text.

### 4.5.3 Rearranging and Relabeling Signals
Our sequential implementations `SEQ` and `SEQ+` only process one instruction at a time, and so there are unique values for signals such as `valC`, `srcA`, and `valE`. In our pipelined design, there will be multiple versions of these values associated with the different instructions flowing through the system. For example, in the detailed structure of `PIPE−`, there are four white boxes labeled `"Stat"` that hold the status codes for four different instructions (see Figure 4.41). We need to take great care to make sure we use the proper version of a signal, or else we could have serious errors, such as storing the result computed for one instruction at the destination register specified by another instruction. We adopt a naming scheme where a signal stored in a pipeline register can be uniquely identified by prefixing its name with that of the pipe register written in uppercase. For example, the four status codes are named `D_stat`, `E_stat`, `M_stat`, and `W_stat`. We also need to refer to some signals that have just been computed within a stage. These are labeled by prefixing the signal name with the first character of the stage name, written in lowercase. Using the status codes as examples, we can see control logic blocks labeled `"Stat"` in the fetch and memory stages. The outputs of these blocks are therefore named `f_stat` and `m_stat`. We can also see that the actual status of the overall processor `Stat` is computed by a block in the write-back stage, based on the status value in pipeline register W.

The decode stages of `SEQ+` and `PIPE−` both generate signals `dstE` and `dstM` indicating the destination register for values` valE` and `valM`. In `SEQ+`, we could connect these signals directly to the address inputs of the register file write ports. With `PIPE−`, these signals are carried along in the pipeline through the execute and memory stages and are directed to the register file only once they reach the write-back stage (shown in the more detailed views of the stages). We do this to make sure the write port address and data inputs hold values from the same instruction. Otherwise, the write back would be writing the values for the instruction in the write-back stage, but with register IDs from the instruction in the decode stage. As a general principle, we want to keep all of the information about a particular instruction contained within a single pipeline stage.

One block of `PIPE−` that is not present in `SEQ+` in the exact same form is the block labeled `"Select A"` in the decode stage. We can see that this block generates the value `valA` for the pipeline register E by choosing either `valP` from pipeline register D or the value read from the A port of the register file. This block is included to reduce the amount of state that must be carried forward to pipeline registers E and M. Of all the different instructions, only the call requires `valP` in the memory stage. Only the jump instructions require the value of `valP` in the execute stage (in the event the jump is not taken). None of these instructions requires a value read from the register file. Therefore, we can reduce the amount of pipeline register state by merging these two signals and carrying them through the pipeline as a single signal `valA`. This eliminates the need for the block labeled "Data" in `SEQ` (Figure 4.23) and `SEQ+` (Figure 4.40), which served a similar purpose. In hardware design, it is common to carefully identify how signals get used and then reduce the amount of register state and wiring by merging signals such as these.

As shown in Figure 4.41, our pipeline registers include a field for the status code `stat`, initially computed during the fetch stage and possibly modified during the memory stage. We will discuss how to implement the processing of exceptional events in Section 4.5.6, after we have covered the implementation of normal instruction execution. Suffice it to say at this point that the most systematic approach is to associate a status code with each instruction as it passes through the pipeline, as we have indicated in the figure.

### 4.5.4 Next PC Prediction

We have taken some measures in the design of `PIPE−` to properly handle control dependencies. Our goal in the pipelined design is to issue a new instruction on every clock cycle, meaning that on each clock cycle, a new instruction proceeds into the execute stage and will ultimately be completed. Achieving this goal would yield a throughput of one instruction per cycle. To do this, we must determine the location of the next instruction right after fetching the current instruction. Unfortunately, if the fetched instruction is a conditional branch, we will not know whether or not the branch should be taken until several cycles later, after the instruction has passed through the execute stage. Similarly, if the fetched instruction is a `ret`, we cannot determine the return location until the instruction has passed through the memory stage.

With the exception of conditional jump instructions and `ret`, we can determine the address of the next instruction based on information computed during the fetch stage. For `call` and `jmp` (unconditional jump), it will be `valC`, the constant word in the instruction, while for all others it will be `valP`, the address of the next instruction. We can therefore achieve our goal of issuing a new instruction every clock cycle in most cases by predicting the next value of the `PC`. For most instruction types, our prediction will be completely reliable. For conditional jumps, we can predict either that a jump will be taken, so that the new `PC` value would be `valC`, or that it will not be taken, so that the new `PC` value would be `valP`. In either case, we must somehow deal with the case where our prediction was incorrect and therefore we have fetched and partially executed the wrong instructions. We will return to this matter in Section 4.5.8.

This technique of guessing the branch direction and then initiating the fetching of instructions according to our guess is known as branch prediction. It is used in some form by virtually all processors. Extensive experiments have been conducted on effective strategies for predicting whether or not branches will be taken. Some systems devote large amounts of hardware to this task. In our design, we will use the simple strategy of predicting that conditional branches are always taken, and so we predict the new value of the `PC` to be `valC`.

We are still left with predicting the new `PC` value resulting from a `ret` instruction. Unlike conditional jumps, we have a nearly unbounded set of possible results, since the return address will be whatever word is on the top of the stack. In our design, ***we will not attempt to predict any value for the return address.*** Instead, we will simply hold off processing any more instructions until the `ret` instruction passes through the write-back stage. We will return to this part of the implementation in Section 4.5.8.

The `PIPE−` fetch stage, diagrammed at the bottom of Figure 4.41, is responsible for both predicting the next value of the `PC` and selecting the actual `PC` for the instruction fetch. We can see the block labeled `"Predict PC"` can choose either `valP` (as computed by the PC incrementer) or `valC` (from the fetched instruction). This value is stored in pipeline register F as the predicted value of the program counter. The block labeled `"Select PC"` is similar to the block labeled `"PC"` in the `SEQ+ PC` selection stage (Figure 4.40). It chooses one of three values to serve as the address for the instruction memory: the predicted PC, the value of `valP` for a not-taken branch instruction that reaches pipeline register M (stored in register `M_valA`), or the value of the return address when a `ret` instruction reaches pipeline register W (stored in `W_valM`).

### 4.5.5 Pipeline Hazards

Our structure `PIPE−` is a good start at creating a pipelined Y86-64 processor. Recall from our discussion in Section 4.4.4, however, that introducing pipelining into a system with feedback can lead to problems when there are dependencies between successive instructions. We must resolve this issue before we can complete our design. These dependencies can take two forms: (1) data dependencies, where the results computed by one instruction are used as the data for a following instruction, and (2) control dependencies, where one instruction determines the location of the following instruction, such as when executing a jump, call, or return. When such dependencies have the potential to cause an erroneous computation by the pipeline, they are called hazards. Like dependencies, hazards can be classified as either data hazards or control hazards. We first concern ourselves with data hazards and then consider control hazards.

Figure 4.43 illustrates the processing of a sequence of instructions we refer to as `prog1` by the `PIPE−` processor. Let us assume in this example and successive ones that the program registers initially all have value 0. The code loads values `10` and `3` into program registers `%rdx` and `%rax`, executes three `nop` instructions, and then adds register `%rdx` to `%rax`. We focus our attention on the potential data hazards resulting from the data dependencies between the two `irmovq` instructions and the `addq` instruction. On the right-hand side of the figure, we show a pipeline diagram for the instruction sequence. The pipeline stages for cycles 6 and 7 are shown highlighted in the pipeline diagram. Below this, we show an expanded view of the write-back activity in cycle 6 and the decode activity during cycle 7. After the start of cycle 7, both of the `irmovq` instructions have passed through the write-back stage, and so the register file holds the updated values of `%rdx` and `%rax`. As the `addq` instruction passes through the decode stage during cycle 7, it will therefore read the correct values for its source operands. The data dependencies between the two `irmovq` instructions and the `addq` instruction have not created data hazards in this example.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703162528769.png" alt="image-20220703162528769" style="zoom:50%;" />

We saw that `prog1` will flow through our pipeline and get the correct results, because the three `nop` instructions create a delay between instructions with data dependencies. Let us see what happens as these `nop` instructions are removed. Figure 4.44 illustrates the pipeline flow of a program, named `prog2`, containing two `nop` instructions between the two `irmovq` instructions generating values for registers `%rdx` and `%rax` and the `addq` instruction having these two registers as operands. In this case, the crucial step occurs in cycle 6, when the `addq` instruction reads its operands from the register file. An expanded view of the pipeline activities during this cycle is shown at the bottom of the figure. The first `irmovq` instruction has passed through the write-back stage, and so program register `%rdx` has been updated in the register file. The second `irmovq` instruction is in the write-back stage during this cycle, and so the write to program register `%rax` only occurs at the start of cycle 7 as the clock rises. As a result, the incorrect value zero would be read for register `%rax` (recall that we assume all registers are initially zero), since the pending write for this register has not yet occurred. Clearly, we will have to adapt our pipeline to handle this hazard properly.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703163210782.png" alt="image-20220703163210782" style="zoom:50%;" />

Figure 4.45 shows what happens when we have only one `nop` instruction between the `irmovq` instructions and the `addq` instruction, yielding a program `prog3`. Now we must examine the behavior of the pipeline during cycle 5 as the `addq` instruction passes through the decode stage. Unfortunately, the pending write to register `%rdx` is still in the write-back stage, and the pending write to `%rax` is still in the memory stage. Therefore, the `addq` instruction would get the incorrect values for both operands.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703163506919.png" alt="image-20220703163506919" style="zoom:50%;" />

Figure 4.46 shows what happens when we remove all of the `nop` instructions between the `irmovq` instructions and the `addq` instruction, yielding a program `prog4`. Now we must examine the behavior of the pipeline during cycle 4 as the `addq` instruction passes through the decode stage. Unfortunately, the pending write to register `%rdx` is still in the memory stage, and the new value for `%rax` is just being computed in the execute stage. Therefore, the `addq` instruction would get the incorrect values for both operands.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703163708498.png" alt="image-20220703163708498" style="zoom:50%;" />

These examples illustrate that a data hazard can arise for an instruction when one of its operands is updated by any of the three preceding instructions. These hazards occur because our pipelined processor reads the operands for an instruction from the register file in the decode stage but does not write the results for the instruction to the register file until three cycles later, after the instruction passes through the write-back stage.

#### Avoiding Data Hazards by Stalling

One very general technique for avoiding hazards involves stalling, where the processor holds back one or more instructions in the pipeline until the hazard condition no longer holds. Our processor can avoid data hazards by holding back an instruction in the decode stage until the instructions generating its source operands have passed through the write-back stage. The details of this mechanism will be discussed in Section 4.5.8. It involves simple enhancements to the pipeline control logic. The effect of stalling is diagrammed in Figure 4.47 (prog2) and Figure 4.48 (prog4). (We omit prog3 from this discussion, since it operates similarly to the other two examples.) When the `addq` instruction is in the decode stage, the pipeline control logic detects that at least one of the instructions in the execute, memory, or write-back stage will update either register `%rdx` or register `%rax`. Rather than letting the `addq` instruction pass through the stage with the incorrect results, it stalls the instruction, holding it back in the decode stage for either one (for prog2) or three (for prog4) extra cycles. For all three programs, the `addq` instruction finally gets correct values for its two source operands in cycle 7 and then proceeds down the pipeline.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220703180025.png" alt="QQ截图20220703180025" style="zoom:50%;" />

In holding back the `addq` instruction in the decode stage, we must also hold back the `halt` instruction following it in the fetch stage. We can do this by keeping the program counter at a fixed value, so that the `halt` instruction will be fetched repeatedly until the stall has completed.

Stalling involves holding back one group of instructions in their stages while allowing other instructions to continue flowing through the pipeline. What then should we do in the stages that would normally be processing the `addq` instruction? We handle these by injecting a bubble into the execute stage each time we hold an instruction back in the decode stage. A bubble is like a dynamically generated `nop` instruction—it does not cause any changes to the registers, the memory, the condition codes, or the program status. These are shown as white boxes in the pipeline diagrams of Figures 4.47 and 4.48. In these figures the arrow between the box labeled "D" for the `addq` instruction and the box labeled "E" for one of the pipeline bubbles indicates that a bubble was injected into the execute stage in place of the `addq` instruction that would normally have passed from the decode to the execute stage. We will look at the detailed mechanisms for making the pipeline stall and for injecting bubbles in Section 4.5.8.

In using stalling to handle data hazards, we effectively execute programs `prog2` and `prog4` by dynamically generating the pipeline flow seen for `prog1` (Figure 4.43). Injecting one bubble for `prog2` and three for `prog4` has the same effect as having three `nop` instructions between the second `irmovq` instruction and the `addq` instruction. This mechanism can be implemented fairly easily (see Problem 4.53), ***but the resulting performance is not very good.*** There are numerous cases in which one instruction updates a register and a closely following instruction uses the same register. This will cause the pipeline to stall for up to three cycles, reducing the overall throughput significantly.

#### Avoiding Data Hazards by Forwarding

Our design for `PIPE−` reads source operands from the register file in the decode stage, but there can also be a pending write to one of these source registers in the write-back stage. Rather than stalling until the write has completed, it can simply pass the value that is about to be written to pipeline register E as the source operand. Figure 4.49 shows this strategy with an expanded view of the pipeline diagram for cycle 6 of `prog2`. The decode-stage logic detects that register `%rax` is the source register for operand `valB`, and that there is also a pending write to `%rax` on write port E. It can therefore avoid stalling by simply using the data word supplied to port E (signal `W_valE`) as the value for operand `valB`. This technique of passing a result value directly from one pipeline stage to an earlier one is commonly known as ***data forwarding*** (or simply forwarding, and sometimes bypassing). It allows the instructions of `prog2` to proceed through the pipeline without any stalling. Data forwarding requires adding additional data connections and control logic to the basic hardware structure.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703181321880.png" alt="image-20220703181321880" style="zoom:50%;" />

As Figure 4.50 illustrates, ***data forwarding can also be used when there is a pending write to a register in the memory stage***, avoiding the need to stall for program `prog3`. In cycle 5, the decode-stage logic detects a pending write to register `%rdx` on port E in the write-back stage, as well as a pending write to register `%rax` that is on its way to port E but is still in the memory stage. Rather than stalling until the writes have occurred, it can use the value in the write-back stage (signal `W_valE`) for operand `valA` and the value in the memory stage (signal `M_valE`) for operand `valB`.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703181944272.png" alt="image-20220703181944272" style="zoom:50%;" />

```antd
const { Popover } = antd
const content = ``
const Comment = () => {
  return (
    <p>
      <Popover content={'充分发挥数据转发的作用'} placement="bottomRight">
        <span className="comments">To exploit data forwarding to its full extent</span></Popover>, we can also pass newly computed values from the execute stage to the decode stage, avoiding the need to stall for program <code>prog4</code>, as illustrated in Figure 4.51. In cycle 4, the decode-stage logic detects a pending write to register <code>%rdx</code> in the memory stage, and also that the value being computed by the ALU in the execute stage will later be written to register <code>%rax</code>. It can use the value in the memory stage (signal <code>M_valE</code>) for operand <code>valA</code>. It can also use the ALU output (signal <code>e_valE</code>) for operand <code>valB</code>. Note that using the ALU output does not introduce any timing problems. The decode stage only needs to generate signals <code>valA</code> and <code>valB</code> by the end of the clock cycle so that pipeline register E can be loaded with the results from the decode stage as the clock rises to start the next cycle. The ALU output will be valid before this point. 
    </p>
  )
}

const root = ReactDOM.createRoot(el)
root.render(<Comment />)
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703183309544.png" alt="image-20220703183309544" style="zoom:50%;" />

The uses of forwarding illustrated in programs `prog2` to `prog4` all involve the forwarding of values generated by the ALU and destined for write port E. Forwarding can also be used with values read from the memory and destined for write port M. From the memory stage, we can forward the value that has just been read from the data memory (signal `m_valM`). From the write-back stage, we can forward the pending write to port M (signal `W_valM`). This gives a total of five different forwarding sources (`e_valE`, `m_valM`, `M_valE`, `W_valM`, and `W_valE`) and two different forwarding destinations (`valA` and `valB`).

The expanded diagrams of Figures 4.49 to 4.51 also show how the decode-stage logic can determine whether to use a value from the register file or to use a forwarded value. Associated with every value that will be written back to the register file is the destination register ID. The logic can compare these IDs with the source register IDs `srcA` and `srcB` to detect a case for forwarding. It is possible to have multiple destination register IDs match one of the source IDs. We must establish a priority among the different forwarding sources to handle such cases. This will be discussed when we look at the detailed design of the forwarding logic.

Figure 4.52 shows the structure of `PIPE`, an extension of `PIPE−` that can handle data hazards by forwarding. Comparing this to the structure of `PIPE−` (Figure 4.41), we can see that the values from the five forwarding sources are fed back to the two blocks labeled `"Sel+Fwd A"` and `"Fwd B"` in the decode stage. The block labeled `"Sel+Fwd A"` combines the role of the block labeled `"Select A"` in `PIPE−` with the forwarding logic. It allows `valA` for pipeline register E to be either the incremented program counter `valP`, the value read from the A port of the register file, or one of the forwarded values. The block labeled `"Fwd B"` implements the forwarding logic for source operand `valB`.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220703184450.png" style="zoom:50%;" />

#### Load/Use Data Hazards

One class of data hazards cannot be handled purely by forwarding, because memory reads occur late in the pipeline. Figure 4.53 illustrates an example of a load/use hazard, where one instruction (the `mrmovq` at address `0x028`) reads a value from memory for register `%rax` while the next instruction (the `addq` at address `0x032`) needs this value as a source operand. Expanded views of cycles 7 and 8 are shown in the lower part of the figure, where we assume all program registers initially have value `0`. The `addq` instruction requires the value of the register in cycle 7, but it is not generated by the `mrmovq` instruction until cycle 8. In order to "forward" from the `mrmovq` to the `addq`, the forwarding logic would have to make the value go backward in time! Since this is clearly impossible, we must find some other mechanism for handling this form of data hazard. (The data hazard for register `%rbx`, with the value being generated by the `irmovq` instruction at address `0x01e` and used by the `addq` instruction at address `0x032`, can be handled by forwarding.)

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703191134341.png" alt="image-20220703191134341" style="zoom:50%;" />

As Figure 4.54 demonstrates, we can avoid a load/use data hazard with a combination of stalling and forwarding. This requires modifications of the control logic, but it can use existing bypass paths. As the `mrmovq` instruction passes through the execute stage, the pipeline control logic detects that the instruction in the decode stage (the `addq`) requires the result read from memory. It stalls the instruction in the decode stage for one cycle, causing a bubble to be injected into the execute stage. As the expanded view of cycle 8 shows, the value read from memory can then be forwarded from the memory stage to the `addq` instruction in the decode stage. The value for register `%rbx` is also forwarded from the writeback to the memory stage. As indicated in the pipeline diagram by the arrow from the box labeled "D" in cycle 7 to the box labeled "E" in cycle 8, the injected bubble replaces the `addq` instruction that would normally continue flowing through the pipeline.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703191813624.png" alt="image-20220703191813624" style="zoom:50%;" />

This use of a stall to handle a load/use hazard is called a load interlock. Load interlocks combined with forwarding suffice to handle all possible forms of data hazards. Since only load interlocks reduce the pipeline throughput, we can nearly achieve our throughput goal of issuing one new instruction on every clock cycle.

#### Avoiding Control Hazards

Control hazards arise when the processor cannot reliably determine the address of the next instruction based on the current instruction in the fetch stage. As was discussed in Section 4.5.4, control hazards can only occur in our pipelined processor for `ret` and `jump` instructions. Moreover, the latter case only causes difficulties when the direction of a conditional jump is mispredicted. In this section, we provide a high-level view of how these hazards can be handled. The detailed implementation will be presented in Section 4.5.8 as part of a more general discussion of the pipeline control.

For the `ret` instruction, consider the following example program. This program is shown in assembly code, but with the addresses of the different instructions on the left for reference:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703193003303.png" alt="image-20220703193003303" style="zoom:50%;" />

<!-- 
```assembly
0x000: 	 irmovq stack,%rsp # Initialize stack pointer
0x00a: 	 call proc         # Procedure call
0x013:   irmovq $10,%rdx   # Return point
0x01d:   halt
0x020: .pos 0x20
0x020: proc:               # proc:
0x020:   ret               # Return immediately
0x021:   rrmovq %rdx,%rbx  # Not executed
0x030: .pos 0x30
0x030: stack:              # stack: Stack pointer
```
-->


Figure 4.55 shows how we want the pipeline to process the ret instruction. As with our earlier pipeline diagrams, this figure shows the pipeline activity with time growing to the right. Unlike before, the instructions are not listed in the same order they occur in the program, since this program involves a control flow where instructions are not executed in a linear sequence. It is useful to look at the instruction addresses to identify the different instructions in the program.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703192732909.png" alt="image-20220703192732909" style="zoom:50%;" />

As this diagram shows, the `ret` instruction is fetched during cycle 3 and proceeds down the pipeline, reaching the write-back stage in cycle 7. While it passes through the decode, execute, and memory stages, the pipeline cannot do any useful activity. Instead, we want to inject three bubbles into the pipeline. Once the `ret` instruction reaches the write-back stage, the PC selection logic will set the program counter to the return address, and therefore the fetch stage will fetch the `irmovq` instruction at the return point (address `0x013`).

To handle a mispredicted branch, consider the following program, shown in assembly code but with the instruction addresses shown on the left for reference:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703193336072.png" alt="image-20220703193336072" style="zoom:50%;" />

Figure 4.56 shows how these instructions are processed. As before, the instructions are listed in the order they enter the pipeline, rather than the order they occur in the program. Since the jump instruction is predicted as being taken, the instruction at the jump target will be fetched in cycle 3, and the instruction following this one will be fetched in cycle 4. By the time the branch logic detects that the jump should not be taken during cycle 4, two instructions have been fetched that should not continue being executed. Fortunately, neither of these instructions has caused a change in the programmer-visible state. That can only occur when an instruction reaches the execute stage, where it can cause the condition codes to change. At this point, the pipeline can simply cancel (sometimes called instruction squashing) the two misfetched instructions by injecting bubbles into the decode and execute stages on the following cycle while also fetching the instruction following the jump instruction. The two misfetched instructions will then simply disappear from the pipeline and therefore not have any effect on the programmer-visible state. The only drawback is that two clock cycles’ worth of instruction processing capability have been wasted.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703193536839.png" alt="image-20220703193536839" style="zoom:50%;" />

This discussion of control hazards indicates that they can be handled by careful consideration of the pipeline control logic. Techniques such as stalling and injecting bubbles into the pipeline dynamically adjust the pipeline flow when special conditions arise. As we will discuss in Section 4.5.8, a simple extension to the basic clocked register design will enable us to stall stages and to inject bubbles into pipeline registers as part of the pipeline control logic.

### 4.5.6 Exception Handling
As we will discuss in Chapter 8, a variety of activities in a processor can lead to exceptional control flow, where the normal chain of program execution gets broken. Exceptions can be generated either internally, by the executing program, or externally, by some outside signal. Our instruction set architecture includes three different internally generated exceptions, caused by (1) a halt instruction, (2) an instruction with an invalid combination of instruction and function code, and (3) an attempt to access an invalid address, either for instruction fetch or data read or write. A more complete processor design would also handle external exceptions, such as when the processor receives a signal that the network interface has received a new packet or the user has clicked a mouse button. Handling exceptions correctly is a challenging aspect of any microprocessor design. They can occur at unpredictable times, and they require creating a clean break in the flow of instructions through the processor pipeline. Our handling of the three internal exceptions gives just a glimpse of the true complexity of correctly detecting and handling exceptions.

Let us refer to the instruction causing the exception as the excepting instruction. In the case of an invalid instruction address, there is no actual excepting instruction, but it is useful to think of there being a sort of "virtual instruction" at the invalid address. In our simplified ISA model, we want the processor to halt when it reaches an exception and to set the appropriate status code, as listed in Figure 4.5. It should appear that all instructions up to the excepting instruction have completed, but none of the following instructions should have any effect on the programmer-visible state. In a more complete design, the processor would continue by invoking an exception handler, a procedure that is part of the operating system, but implementing this part of exception handling is beyond the scope of our presentation.

In a pipelined system, exception handling involves several subtleties. First, it is possible to have exceptions triggered by multiple instructions simultaneously. For example, during one cycle of pipeline operation, we could have a halt instruction in the fetch stage, and the data memory could report an out-of-bounds data address for the instruction in the memory stage. We must determine which of these exceptions the processor should report to the operating system. The basic rule is to put priority on the exception triggered by the instruction that is furthest along the pipeline. In the example above, this would be the out-of-bounds address attempted by the instruction in the memory stage. In terms of the machine-language program, the instruction in the memory stage should appear to execute before one in the fetch stage, and therefore only this exception should be reported to the operating system.

A second subtlety occurs when an instruction is first fetched and begins execution, causes an exception, and later is canceled due to a mispredicted branch. The following is an example of such a program in its object-code form:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703202821136.png" alt="image-20220703202821136" style="zoom:50%;" />

In this program, the pipeline will predict that the branch should be taken, and so it will fetch and attempt to use a byte with value `0xFF` as an instruction (generated in the assembly code using the `.byte` directive). The decode stage will therefore detect an invalid instruction exception. Later, the pipeline will discover that the branch should not be taken, and so the instruction at address `0x016` should never even have been fetched. The pipeline control logic will cancel this instruction, but we want to avoid raising an exception.

A third subtlety arises because a pipelined processor updates different parts of the system state in different stages. It is possible for an instruction following one causing an exception to alter some part of the state before the excepting instruction completes. For example, consider the following code sequence, in which we assume that user programs are not allowed to access addresses at the upper end of the 64-bit range:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703203147102.png" alt="image-20220703203147102" style="zoom:50%;" />

The `pushq` instruction causes an address exception, because decrementing the stack pointer causes it to wrap around to `0xfffffffffffffff8`. This exception is detected in the memory stage. On the same cycle, the `addq` instruction is in the execute stage, and it will cause the condition codes to be set to new values. This would violate our requirement that none of the instructions following the excepting instruction should have had any effect on the system state.

In general, we can both correctly choose among the different exceptions and avoid raising exceptions for instructions that are fetched due to mispredicted branches by merging the exception-handling logic into the pipeline structure. That is the motivation for us to include a status code stat in each of our pipeline registers (Figures 4.41 and 4.52). If an instruction generates an exception at some stage in its processing, the status field is set to indicate the nature of the exception. The exception status propagates through the pipeline with the rest of the information for that instruction, until it reaches the write-back stage. At this point, the pipeline control logic detects the occurrence of the exception and stops execution.

To avoid having any updating of the programmer-visible state by instructions beyond the excepting instruction, the pipeline control logic must disable any updating of the condition code register or the data memory when an instruction in the memory or write-back stages has caused an exception. In the example program above, the control logic will detect that the `pushq` in the memory stage has caused an exception, and therefore the updating of the condition code register by the `addq` instruction in the execute stage will be disabled.

Let us consider how this method of handling exceptions deals with the subtleties we have mentioned. When an exception occurs in one or more stages of a pipeline, the information is simply stored in the status fields of the pipeline registers. ***The event has no effect on the flow of instructions in the pipeline until an excepting instruction reaches the final pipeline stage***, except to disable any updating of the programmer-visible state (the condition code register and the memory) by later instructions in the pipeline. 

Since instructions reach the write-back stage in the same order as they would be executed in a nonpipelined processor, we are guaranteed that the first instruction encountering an exception will arrive first in the write-back stage, at which point program execution can stop and the status code in pipeline register W can be recorded as the program status. If some instruction is fetched but later canceled, any exception status information about the instruction gets canceled as well. No instruction following one that causes an exception can alter the programmer-visible state. The simple rule of carrying the exception status together with all other information about an instruction through the pipeline provides a simple and reliable mechanism for handling exceptions.

### 4.5.7 PIPE Stage Implementations

We have now created an overall structure for `PIPE`, our pipelined Y86-64 processor with forwarding. It uses the same set of hardware units as the earlier sequential designs, with the addition of pipeline registers, some reconfigured logic blocks, and additional pipeline control logic. In this section, we go through the design of the different logic blocks, deferring the design of the pipeline control logic to the next section. Many of the logic blocks are identical to their counterparts in `SEQ` and `SEQ+`, except that we must choose proper versions of the different signals from the pipeline registers (written with the pipeline register name, written in uppercase, as a prefix) or from the stage computations (written with the first character of the stage name, written in lowercase, as a prefix).

As an example, compare the `HCL` code for the logic that generates the `srcA` signal in `SEQ` to the corresponding code in `PIPE`:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220703210810410.png" alt="image-20220703210810410" style="zoom:50%;" />

They differ only in the prefixes added to the PIPE signals: `D_` for the source values, to indicate that the signals come from pipeline register D, and `d_` for the result value, to indicate that it is generated in the decode stage. To avoid repetition, we will not show the HCL code here for blocks that only differ from those in SEQ because of the prefixes on names. 

#### PC Selection and Fetch Stage

Figure 4.57 provides a detailed view of the `PIPE` fetch stage logic. As discussed earlier, this stage must also select a current value for the program counter and predict the next `PC` value. The hardware units for reading the instruction from memory and for extracting the different instruction fields are the same as those we considered for `SEQ` (see the fetch stage in Section 4.3.4).

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705112225305.png" alt="image-20220705112225305" style="zoom:50%;" />

The `PC` selection logic chooses between three program counter sources. As a mispredicted branch enters the memory stage, the value of `valP` for this instruction (indicating the address of the following instruction) is read from pipeline register M (signal `M_valA`). When a `ret` instruction enters the write-back stage, the return address is read from pipeline register W (signal `W_valM`). All other cases use the predicted value of the PC, stored in pipeline register F (signal `F_predPC`):

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705124140410.png" alt="image-20220705124140410" style="zoom:50%;" />

The PC prediction logic chooses valC for the fetched instruction when it is either a call or a jump, and valP otherwise:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705124346900.png" alt="image-20220705124346900" style="zoom:50%;" />

The logic blocks labeled `"Instr valid"`, `"Need regids"`, and `"Need valC"` are the same as for `SEQ`, with appropriately named source signals. 

Unlike in `SEQ`, we must split the computation of the instruction status into two parts. In the fetch stage, we can test for a memory error due to an out-of-range instruction address, and we can detect an illegal instruction or a halt instruction. Detecting an invalid data address must be deferred to the memory stage.

#### Decode and Write-Back Stages

Figure 4.58 gives a detailed view of the decode and write-back logic for `PIPE`. The blocks labeled `dstE`, `dstM`, `srcA`, and `srcB` are very similar to their counterparts in the implementation of `SEQ`. Observe that the register IDs supplied to the write ports come from the write-back stage (signals `W_dstE` and `W_dstM`), rather than from the decode stage. This is because we want the writes to occur to the destination registers specified by the instruction in the write-back stage.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220705125732.png" alt="QQ截图20220705125732" style="zoom:50%;" />

Most of the complexity of this stage is associated with the forwarding logic. As mentioned earlier, the block labeled `"Sel+Fwd A"` serves two roles. It merges the `valP` signal into the `valA` signal for later stages in order to reduce the amount of state in the pipeline register. It also implements the forwarding logic for source operand `valA`.

The merging of signals `valA` and `valP` exploits the fact that only the `call` and `jump` instructions need the value of `valP` in later stages, and these instructions do not need the value read from the A port of the register file. This selection is controlled by the `icode` signal for this stage. When signal `D_icode` matches the instruction code for either `call` or `jXX`, this block should select `D_valP` as its output.

As mentioned in Section 4.5.5, there are five different forwarding sources, each with a data word and a destination register ID:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705132712754.png" alt="image-20220705132712754" style="zoom:50%;" />

If none of the forwarding conditions hold, the block should select `d_rvalA`, the value read from register port A, as its output.

Putting all of this together, we get the following `HCL` description for the new value of `valA` for pipeline register E:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705132834035.png" alt="image-20220705132834035" style="zoom:50%;" />

The priority given to the five forwarding sources in the above `HCL` code is very important. This priority is determined in the `HCL` code by the order in which the five destination register IDs are tested. ***If any order other than the one shown were chosen, the pipeline would behave incorrectly for some programs.*** Figure 4.59 shows an example of a program that requires a correct setting of priority among the forwarding sources in the execute and memory stages. 
<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705155437118.png" alt="image-20220705155437118" style="zoom:50%;" />
In this program, the first two instructions write to register `%rdx`, while the third uses this register as its source operand. When the `rrmovq` instruction reaches the decode stage in cycle 4, the forwarding logic must choose between two values destined for its source register. Which one should it choose? To set the priority, we must consider the behavior of the machine-language program when it is executed one instruction at a time. The first `irmovq` instruction would set register `%rdx` to `10`, the second would set the register to `3`, and then the `rrmovq` instruction would read `3` from `%rdx`. ***To imitate this behavior, our pipelined implementation should always give priority to the forwarding source in the earliest pipeline stage, since it holds the latest instruction in the program sequence setting the register.*** Thus, the logic in the `HCL` code above first tests the forwarding source in the execute stage, then those in the memory stage, and finally the sources in the write-back stage. The forwarding priority between the two sources in either the memory or the write-back stages is only a concern for the instruction `popq %rsp`, since only this instruction can attempt two simultaneous writes to the same register.
```antd
const { Popover } = antd
const Comment = () => {
  return (
	<p>
  	One small part of the write-back stage remains. As shown in <Popover content={() => <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/QQ截图20220703184450.png" style={{zoom: '35%'}} />}><span className="comments">Figure 4.52</span></Popover>, the overall processor status <code>Stat</code> is computed by a block based on the status value in pipeline register W. Recall from Section 4.1.1 that the code should indicate either normal operation (AOK) or one of the three exception conditions. Since pipeline register W holds the state of the most recently completed instruction, it is natural to use this value as an indication of the overall processor status. The only special case to consider is when there is a bubble in the write-back stage. This is part of normal operation, and so we want the status code to be AOK for this case as well:
	</p>
  )
}

const root = ReactDOM.createRoot(el)
root.render(<Comment />)
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705161042821.png" alt="image-20220705161042821" style="zoom:50%;" />

#### Execute Stage

Figure 4.60 shows the execute stage logic for `PIPE`. The hardware units and the logic blocks are identical to those in `SEQ`, with an appropriate renaming of signals. We can see the signals `e_valE` and `e_dstE` directed toward the decode stage as one of the forwarding sources. One difference is that the logic labeled `"Set CC"`, which determines whether or not to update the condition codes, has signals `m_stat` and `W_stat` as inputs. These signals are used to detect cases where an instruction causing an exception is passing through later pipeline stages, and therefore any updating of the condition codes should be suppressed. This aspect of the design is discussed in Section 4.5.8.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705161411818.png" alt="image-20220705161411818" style="zoom:50%;" />

#### Memory Stage

Figure 4.61 shows the memory stage logic for `PIPE`. Comparing this to the memory stage for `SEQ` (Figure 4.30), we see that, as noted before, the block labeled `"Mem.data"` in `SEQ` is not present in `PIPE`. This block served to select between data sources `valP` (for call instructions) and `valA`, but this selection is now performed by the block labeled `"Sel+Fwd A"` in the decode stage. Most other blocks in this stage are identical to their counterparts in `SEQ`, with an appropriate renaming of the signals. In this figure, you can also see that many of the values in pipeline registers and M and W are supplied to other parts of the circuit as part of the forwarding and pipeline control logic.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705161639748.png" alt="image-20220705161639748" style="zoom:50%;" />

### 4.5.8 Pipeline Control Logic
We are now ready to complete our design for `PIPE` by creating the pipeline control logic. This logic must handle the following four control cases for which other mechanisms, such as data forwarding and branch prediction, do not suffice:

- ***Load/use hazards.*** The pipeline must stall for one cycle between an instruction that reads a value from memory and an instruction that uses this value.
- ***Processing ret.*** The pipeline must stall until the `ret` instruction reaches the write-back stage.
- ***Mispredicted branches.*** By the time the branch logic detects that a `jump` should not have been taken, several instructions at the branch target will have started down the pipeline. These instructions must be canceled, and fetching should begin at the instruction following the `jump` instruction.
- ***Exceptions.*** When an instruction causes an exception, we want to disable the updating of the programmer-visible state by later instructions and halt execution once the excepting instruction reaches the write-back stage.

We will go through the desired actions for each of these cases and then develop control logic to handle all of them.

#### Desired Handling of Special Control Cases

For a load/use hazard, we have described the desired pipeline operation in Section 4.5.5, as illustrated by the example of Figure 4.54. Only the `mrmovq` and `popq` instructions read data from memory. When (1) either of these is in the execute stage and (2) an instruction requiring the destination register is in the decode stage, we want to hold back the second instruction in the decode stage and inject a bubble into the execute stage on the next cycle. After this, the forwarding logic will resolve the data hazard. The pipeline can hold back an instruction in the decode stage by keeping pipeline register D in a fixed state. In doing so, it should also keep pipeline register F in a fixed state, so that the next instruction will be fetched a second time. In summary, implementing this pipeline flow requires detecting the hazard condition, keeping pipeline registers F and D fixed, and injecting a bubble into the execute stage.

For the processing of a `1` instruction, we have described the desired pipeline operation in Section 4.5.5. The pipeline should stall for three cycles until the return address is read as the `ret` instruction passes through the memory stage. This was illustrated by a simplified pipeline diagram in Figure 4.55 for processing the following program:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705164507136.png" alt="image-20220705164507136" style="zoom:50%;" />

Figure 4.62 provides a detailed view of the processing of the `ret` instruction for the example program. The key observation here is that there is no way to inject a bubble into the fetch stage of our pipeline. On every cycle, the fetch stage reads some instruction from the instruction memory. Looking at the `HCL` code for implementing the `PC` prediction logic in Section 4.5.7, we can see that for the `ret` instruction, the new value of the `PC` is predicted to be `valP`, the address of the following instruction. In our example program, this would be `0x021`, the address of the `rrmovq` instruction following the `ret`. This prediction is not correct for this example, nor would it be for most cases, but we are not attempting to predict return addresses correctly in our design. For three clock cycles, the fetch stage stalls, causing the `rrmovq` instruction to be fetched but then replaced by a bubble in the decode stage. This process is illustrated in Figure 4.62 by the three fetches, with an arrow leading down to the bubbles passing through the remaining pipeline stages. Finally, the `irmovq` instruction is fetched on cycle 7. Comparing Figure 4.62 with Figure 4.55, we see that our implementation achieves the desired effect, but with a slightly peculiar fetching of an incorrect instruction for three consecutive cycles.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705164555032.png" alt="image-20220705164555032" style="zoom:50%;" />

When a mispredicted branch occurs, we have described the desired pipeline operation in Section 4.5.5 and illustrated it in Figure 4.56. The misprediction will be detected as the `jump` instruction reaches the execute stage. The control logic then injects bubbles into the decode and execute stages on the next cycle, causing the two incorrectly fetched instructions to be canceled. On the same cycle, the pipeline reads the correct instruction into the fetch stage.

For an instruction that causes an exception, we must make the pipelined implementation match the desired ISA behavior, with all prior instructions completing and with none of the following instructions having any effect on the program state. Achieving these effects is complicated by the facts that (1) exceptions are detected during two different stages (fetch and memory) of program execution, and (2) the program state is updated in three different stages (execute, memory, and write-back).

Our stage designs include a status code `stat` in each pipeline register to track the status of each instruction as it passes through the pipeline stages. When an exception occurs, we record that information as part of the instruction’s status and continue fetching, decoding, and executing instructions as if nothing were amiss. As the excepting instruction reaches the memory stage, we take steps to prevent later instructions from modifying the programmer-visible state by (1) disabling the setting of condition codes by instructions in the execute stage, (2) injecting bubbles into the memory stage to disable any writing to the data memory, and (3) stalling the write-back stage when it has an excepting instruction, thus bringing the pipeline to a halt.

The pipeline diagram in Figure 4.63 illustrates how our pipeline control handles the situation where an instruction causing an exception is followed by one that would change the condition codes. On cycle 6, the `pushq` instruction reaches the memory stage and generates a memory error. On the same cycle, the `addq` instruction in the execute stage generates new values for the condition codes. We disable the setting of condition codes when an excepting instruction is in the memory or write-back stage (by examining the signals `m_stat` and `W_stat` and then setting the signal `set_cc` to zero). We can also see the combination of injecting bubbles into the memory stage and stalling the excepting instruction in the write-back stage in the example of Figure 4.63—the `pushq` instruction remains stalled in the write-back stage, and none of the subsequent instructions get past the execute stage.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705165537269.png" alt="image-20220705165537269" style="zoom:50%;" />

By this combination of pipelining the status signals, controlling the setting of condition codes, and controlling the pipeline stages, we achieve the desired behavior for exceptions: all instructions prior to the excepting instruction are completed, while none of the following instructions has any effect on the programmer-visible state.

#### Detecting Special Control Conditions

Figure 4.64 summarizes the conditions requiring special pipeline control. It gives expressions describing the conditions under which the three special cases arise. These expressions are implemented by simple blocks of combinational logic that must generate their results before the end of the clock cycle in order to control the action of the pipeline registers as the clock rises to start the next cycle. 

During a clock cycle, pipeline registers D, E, and M hold the states of the instructions that are in the decode, execute, and memory pipeline stages, respectively. As we approach the end of the clock cycle, signals `d_srcA` and `d_srcB` will be set to the register IDs of the source operands for the instruction in the decode stage. 

Detecting a `ret` instruction as it passes through the pipeline simply involves checking the instruction codes of the instructions in the decode, execute, and memory stages. Detecting a load/use hazard involves checking the instruction type (`mrmovq` or `popq`) of the instruction in the execute stage and comparing its destination register with the source registers of the instruction in the decode stage. The pipeline control logic should detect a mispredicted branch while the `jump` instruction is in the execute stage, so that it can set up the conditions required to recover from the misprediction as the instruction enters the memory stage. When a `jump` instruction is in the execute stage, the signal `e_Cnd` indicates whether or not the `jump` should be taken. We detect an excepting instruction by examining the instruction status values in the memory and write-back stages. For the memory stage, we use the signal `m_stat`, computed within the stage, rather than `M_stat` from the pipeline register. This internal signal incorporates the possibility of a data memory address error.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705165919062.png" alt="image-20220705165919062" style="zoom:50%;" />

#### Pipeline Control Mechanisms

Figure 4.65 shows low-level mechanisms that allow the pipeline control logic to hold back an instruction in a pipeline register or to inject a bubble into the pipeline. These mechanisms involve small extensions to the basic clocked register described in Section 4.2.5. 

Suppose that each pipeline register has two control inputs stall and bubble. The settings of these signals determine how the pipeline register is updated as the clock rises. Under normal operation (Figure 4.65(a)), both of these inputs are set to `0`, causing the register to load its input as its new state. When the stall signal is set to `1` (Figure 4.65(b)), the updating of the state is disabled. Instead, the register will remain in its previous state. This makes it possible to hold back an instruction in some pipeline stage. When the bubble signal is set to `1` (Figure 4.65(c)), the state of the register will be set to some fixed reset configuration, giving a state equivalent to that of a `nop` instruction. 

The particular pattern of ones and zeros for a pipeline register’s reset configuration depends on the set of fields in the pipeline register. For example, to inject a bubble into pipeline register D, we want the `icode` field to be set to the constant value `INOP` (Figure 4.26). To inject a bubble into pipeline register E, we want the `icode` field to be set to `INOP` and the `dstE`, `dstM`, `srcA`, and `srcB` fields to be set to the constant `RNONE`. Determining the reset configuration is one of the tasks for the hardware designer in designing a pipeline register. We will not concern ourselves with the details here. We will consider it an error to set both the bubble and the stall signals to `1`.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705170627976.png" alt="image-20220705170627976" style="zoom:50%;" />

The table in Figure 4.66 shows the actions the different pipeline stages should take for each of the three special conditions. Each involves some combination of normal, stall, and bubble operations for the pipeline registers. In terms of timing, the stall and bubble control signals for the pipeline registers are generated by blocks of combinational logic. These values must be valid as the clock rises, causing each of the pipeline registers to either load, stall, or bubble as the next clock cycle begins. With this small extension to the pipeline register designs, we can implement a complete pipeline, including all of its control, using the basic building blocks of combinational logic, clocked registers, and random access memories.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705171027754.png" alt="image-20220705171027754" style="zoom:50%;" />

#### Combinations of Control Conditions

In our discussion of the special pipeline control conditions so far, we assumed that at most one special case could arise during any single clock cycle. A common bug in designing a system is to fail to handle instances where multiple special conditions arise simultaneously. 

Let us analyze such possibilities. We need not worry about combinations involving program exceptions, since we have carefully designed our exception-handling mechanism to consider other instructions in the pipeline. Figure 4.67 diagrams the pipeline states that cause the other three special control conditions. These diagrams show blocks for the decode, execute, and memory stages. The shaded boxes represent particular constraints that must be satisfied for the condition to arise. 

A load/use hazard requires that the instruction in the execute stage reads a value from memory into a register, and that the instruction in the decode stage has this register as a source operand. A mispredicted branch requires the instruction in the execute stage to have a jump instruction. There are three possible cases for ret—the instruction can be in either the decode, execute, or memory stage. As the `ret` instruction moves through the pipeline, the earlier pipeline stages will have bubbles.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705171305307.png" alt="image-20220705171305307" style="zoom:50%;" />

We can see by these diagrams that most of the control conditions are mutually exclusive. For example, it is not possible to have a `load/use` hazard and a mispredicted branch simultaneously, since one requires a load instruction (`mrmovq` or `popq`) in the execute stage, while the other requires a `jump`. Similarly, the second and third `ret` combinations cannot occur at the same time as a `load/use` hazard or a mispredicted branch. Only the two combinations indicated by arrows can arise simultaneously.

Combination A involves a not-taken jump instruction in the execute stage and a `ret` instruction in the decode stage. Setting up this combination requires the `ret` to be at the target of a not-taken branch. The pipeline control logic should detect that the branch was mispredicted and therefore cancel the `ret` instruction.

Combining the control actions for the combination A conditions (Figure 4.66), we get the following pipeline control actions (assuming that either a bubble or a stall overrides the normal case):

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705172035675.png" alt="image-20220705172035675" style="zoom:50%;" />

That is, it would be handled like a mispredicted branch, but with a stall in the fetch stage. Fortunately, on the next cycle, the PC selection logic will choose the address of the instruction following the `jump`, rather than the predicted program counter, and so it does not matter what happens with the pipeline register F. We conclude that the pipeline will correctly handle this combination.

Combination B involves a `load/use` hazard, where the loading instruction sets register `%rsp` and the `ret` instruction then uses this register as a source operand, since it must pop the return address from the stack. The pipeline control logic should hold back the `ret` instruction in the decode stage.

Combining the control actions for the combination B conditions (Figure 4.66), we get the following pipeline control actions:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705172415935.png" alt="image-20220705172415935" style="zoom:50%;" />

If both sets of actions were triggered, the control logic would try to stall the `ret` instruction to avoid the `load/use` hazard but also inject a bubble into the decode stage due to the `ret` instruction. Clearly, we do not want the pipeline to perform both sets of actions. Instead, we want it to just take the actions for the `load/use` hazard. The actions for processing the `ret` instruction should be delayed for one cycle.

This analysis shows that combination B requires special handling. In fact, our original implementation of the `PIPE` control logic did not handle this combination correctly. Even though the design had passed many simulation tests, it had a subtle bug that was uncovered only by the analysis we have just shown. When a program having combination B was executed, the control logic would set both the bubble and the stall signals for pipeline register D to `1`. This example shows the importance of systematic analysis. It would be unlikely to uncover this bug by just running normal programs. If left undetected, the pipeline would not faithfully implement the ISA behavior.

#### Control Logic Implementation

Figure 4.68 shows the overall structure of the pipeline control logic. Based on signals from the pipeline registers and pipeline stages, the control logic generates stall and bubble control signals for the pipeline registers and also determines whether the condition code registers should be updated. We can combine the detection conditions of Figure 4.64 with the actions of Figure 4.66 to create `HCL` descriptions for the different pipeline control signals.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705172804159.png" alt="image-20220705172804159" style="zoom:50%;" />

Pipeline register F must be stalled for either a `load/use` hazard or a `ret` instruction:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705173011231.png" alt="image-20220705173011231" style="zoom:50%;" />

Pipeline register D must be set to bubble for a mispredicted branch or a `ret` instruction. As the analysis in the preceding section shows, however, it should not inject a bubble when there is a `load/use hazard` in combination with a `ret` instruction:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705173247481.png" alt="image-20220705173247481" style="zoom:50%;" />

### 4.5.9 Performance Analysis

We can see that the conditions requiring special action by the pipeline control logic all cause our pipeline to fall short of the goal of issuing a new instruction on every clock cycle. We can measure this inefficiency by determining how often a bubble gets injected into the pipeline, since these cause unused pipeline cycles. A return instruction generates three bubbles, a load/use hazard generates one, and a mispredicted branch generates two. We can quantify the effect these penalties have on the overall performance by computing an estimate of the average number of clock cycles PIPE would require per instruction it executes, a measure known as the CPI (for "cycles per instruction"). This measure is the reciprocal of the average throughput of the pipeline, but with time measured in clock cycles rather than picoseconds. It is a useful measure of the architectural efficiency of a design.

If we ignore the performance implications of exceptions (which, by definition, will only occur rarely), another way to think about CPI is to imagine we run the processor on some benchmark program and observe the operation of the execute stage. On each cycle, the execute stage either (1) processes an instruction and this instruction continues through the remaining stages to completion, or (2) processes a bubble injected due to one of the three special cases. If the stage processes a total of $C_i$ instructions and $C_b$ bubbles, then the processor has required around $C_i + C_b$ total clock cycles to execute $C_i$ instructions. We say "around" because we ignore the cycles required to start the instructions flowing through the pipeline. We can then compute the CPI for this benchmark as follows:
$$
\text{CPI} = \frac{C_i + C_b}{C_i} = 1.0 + \frac{C_b}{C_i}
$$
That is, the CPI equals 1.0 plus a penalty term $C_b/C_i$ indicating the average number of bubbles injected per instruction executed. Since only three different instruction types can cause a bubble to be injected, we can break this penalty term into three components:
$$
\text{CPI} = 1.0 + lp + mp + rp
$$
where $lp$ (for "load penalty") is the average frequency with which bubbles are injected while stalling for load/use hazards, $mp$ (for "mispredicted branch penalty") is the average frequency with which bubbles are injected when canceling instructions due to mispredicted branches, and $rp$ (for "return penalty") is the average frequency with which bubbles are injected while stalling for ret instructions. Each of these penalties indicates the total number of bubbles injected for the stated reason (some portion of Cb) divided by the total number of instructions that were executed (Ci).

To estimate each of these penalties, we need to know how frequently the relevant instructions (load, conditional branch, and return) occur, and for each of these how frequently the particular condition arises. Let us pick the following set of frequencies for our CPI computation:

- Load instructions (`mrmovq` and `popq`) account for 25% of all instructions executed. Of these, 20% cause load/use hazards.
- Conditional branches account for 20% of all instructions executed. Of these, 60% are taken and 40% are not taken.
- Return instructions account for 2% of all instructions executed.

We can therefore estimate each of our penalties as the product of the frequency of the instruction type, the frequency the condition arises, and the number of bubbles that get injected when the condition occurs:

![image-20220705193308342](https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-20220705193308342.png)

The sum of the three penalties is 0.27, giving a CPI of 1.27.

Our goal was to design a pipeline that can issue one instruction per cycle, giving a CPI of 1.0. We did not quite meet this goal, but the overall performance is still quite good. We can also see that any effort to reduce the CPI further should focus on mispredicted branches. They account for 0.16 of our total penalty of 0.27, because conditional branches are common, our prediction strategy often fails, and we cancel two instructions for every misprediction.

### 4.5.10 Unfinished Business
We have created a structure for the `PIPE` pipelined microprocessor, designed the control logic blocks, and implemented pipeline control logic to handle special cases where normal pipeline flow does not suffice. Still, `PIPE` lacks several key features that would be required in an actual microprocessor design. We highlight a few of these and discuss what would be required to add them.

#### Multicycle Instructions

All of the instructions in the Y86-64 instruction set involve simple operations such as adding numbers. These can be processed in a single clock cycle within the execute stage. In a more complete instruction set, we would also need to implement instructions requiring more complex operations such as integer multiplication and division and floating-point operations. In a medium-performance processor such as `PIPE`, typical execution times for these operations range from 3 or 4 cycles for floating-point addition up to 64 cycles for integer division. To implement these instructions, we require both additional hardware to perform the computations and a mechanism to coordinate the processing of these instructions with the rest of the pipeline.

One simple approach to implementing multicycle instructions is to simply expand the capabilities of the execute stage logic with integer and floating-point arithmetic units. An instruction remains in the execute stage for as many clock cycles as it requires, causing the fetch and decode stages to stall. This approach is simple to implement, but the resulting performance is not very good.

Better performance can be achieved by handling the more complex operations with special hardware functional units that operate independently of the main pipeline. Typically, there is one functional unit for performing integer multiplication and division, and another for performing floating-point operations. As an instruction enters the decode stage, it can be issued to the special unit. While the unit performs the operation, the pipeline continues processing other instructions. Typically, the floating-point unit is itself pipelined, and thus multiple operations can execute concurrently in the main pipeline and in the different units.

The operations of the different units must be synchronized to avoid incorrect behavior. For example, if there are data dependencies between the different operations being handled by different units, the control logic may need to stall one part of the system until the results from an operation handled by some other part of the system have been completed. Often, different forms of forwarding are used to convey results from one part of the system to other parts, just as we saw between the different stages of `PIPE`. The overall design becomes more complex than we have seen with `PIPE`, but the same techniques of stalling, forwarding, and pipeline control can be used to make the overall behavior match the sequential ISA model.

#### Interfacing with the Memory System

In our presentation of `PIPE`, we assumed that both the instruction fetch unit and the data memory could read or write any memory location in one clock cycle. We also ignored the possible hazards caused by self-modifying code where one instruction writes to the region of memory from which later instructions are fetched. Furthermore, we reference memory locations according to their virtual addresses, and these require a translation into physical addresses before the actual read or write operation can be performed. Clearly, it is unrealistic to do all of this processing in a single clock cycle. Even worse, the memory values being accessed may reside on disk, requiring millions of clock cycles to read into the processor memory.

As will be discussed in Chapters 6 and 9, the memory system of a processor uses a combination of multiple hardware memories and operating system software to manage the virtual memory system. The memory system is organized as a hierarchy, with faster but smaller memories holding a subset of the memory being backed up by slower and larger memories. At the level closest to the processor, the cache memories provide fast access to the most heavily referenced memory locations. A typical processor has two first-level caches—one for reading instructions and one for reading and writing data. Another type of cache memory, known as a translation look-aside buffer, or TLB, provides a fast translation from virtual to physical addresses. ***Using a combination of TLBs and caches, it is indeed possible to read instructions and read or write data in a single clock cycle most of the time.*** Thus, our simplified view of memory referencing by our processors is actually quite reasonable.

Although the caches hold the most heavily referenced memory locations, there will be times when a cache miss occurs, where some reference is made to a location that is not held in the cache. In the best case, the missing data can be retrieved from a higher-level cache or from the main memory of the processor, ***requiring 3 to 20 clock cycles***. Meanwhile, the pipeline simply stalls, holding the instruction in the fetch or memory stage until the cache can perform the read or write operation. In terms of our pipeline design, this can be implemented by adding more stall conditions to the pipeline control logic. A cache miss and the consequent synchronization with the pipeline is handled completely by hardware, keeping the time required down to a small number of clock cycles.

In some cases, the memory location being referenced is actually stored in the disk or nonvolatile memory. When this occurs, the hardware signals a page fault exception. Like other exceptions, this will cause the processor to invoke the operating system’s exception handler code. This code will then set up a transfer from the disk to the main memory. Once this completes, the operating system will return to the original program, where the instruction causing the page fault will be re-executed. This time, the memory reference will succeed, although it might cause a cache miss. Having the hardware invoke an operating system routine, which then returns control back to the hardware, allows the hardware and system software to cooperate in the handling of page faults. Since ***accessing a disk can require millions of clock cycles***, the several thousand cycles of processing performed by the OS page fault handler has little impact on performance.

From the perspective of the processor, the combination of stalling to handle short-duration cache misses and exception handling to handle long-duration page faults takes care of any unpredictability in memory access times due to the structure of the memory hierarchy.

## 4.6 Summary

We have seen that the instruction set architecture, or ISA, provides a layer of abstraction between the behavior of a processor—in terms of the set of instructions and their encodings—and how the processor is implemented. The ISA provides a very sequential view of program execution, with one instruction executed to completion before the next one begins.

We defined the Y86-64 instruction set by starting with the x86-64 instructions and simplifying the data types, address modes, and instruction encoding considerably. The resulting ISA has attributes of both `RISC` and `CISC` instruction sets. We then organized the processing required for the different instructions into a series of five stages, where the operations at each stage vary according to the instruction being executed. From this, we constructed the `SEQ` processor, in which an entire instruction is executed every clock cycle by having it flow through all five stages.

Pipelining improves the throughput performance of a system by letting the different stages operate concurrently. At any given time, multiple operations are being processed by the different stages. In introducing this concurrency, we must be careful to provide the same program-level behavior as would a sequential execution of the program. We introduced pipelining by reordering parts of `SEQ` to get `SEQ+` and then adding pipeline registers to create the `PIPE−` pipeline. We enhanced the pipeline performance by adding forwarding logic to speed the sending of a result from one instruction to another. Several special cases require additional pipeline control logic to stall or cancel some of the pipeline stages.

```antd
const { Popover } = antd
const content = `只有到异常指令为止的指令会影响到程序员可见的状态。`
const Comment = () => {
  return (
	<p>
	  Our design included <Popover content='底层的'><span className="comments">rudimentary</span></Popover> mechanisms to handle exceptions, where we make sure that <Popover content={content}><span className="comments">only instructions up to the excepting instruction affect the programmer-visible state.</span></Popover> Implementing a complete handling of exceptions would be significantly more challenging. Properly handling exceptions gets even more complex in systems that employ greater degrees of pipelining and parallelism.  
	</p>
  )
}

const root = ReactDOM.createRoot(el)
root.render(<Comment />)
```

In this chapter, we have learned several important lessons about processor design:

- ***Managing complexity is a top priority.*** We want to make optimum use of the hardware resources to get maximum performance at minimum cost. We did this by creating a very simple and uniform framework for processing all of the different instruction types. With this framework, we could share the hardware units among the logic for processing the different instruction types.
- ***We do not need to implement the ISA directly.*** A direct implementation of the ISA would imply a very sequential design. To achieve higher performance, we want to exploit the ability in hardware to perform many operations simultaneously. This led to the use of a pipelined design. By careful design and analysis, we can handle the various pipeline hazards, so that the overall effect of running a program exactly matches what would be obtained with the ISA model.
- ***Hardware designers must be meticulous.*** Once a chip has been fabricated, it is nearly impossible to correct any errors. It is very important to get the design right on the first try. This means carefully analyzing different instruction types and combinations, even ones that do not seem to make sense, such as popping to the stack pointer. Designs must be thoroughly tested with systematic simulation test programs. In developing the control logic for `PIPE`, our design had a subtle bug that was uncovered only after a careful and systematic analysis of control combinations.
