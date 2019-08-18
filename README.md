# GSoC19
Google Summer of Code - 2019, Honeynet Organization, Project Drakvuf


Injecting function-calls to Linux through a hypervisor
======================================================

This article is part of my final evaluation for the Google Summer of Code 2019

Links to my work:
- <https://github.com/tklengyel/drakvuf/pull/666>


TEAM
----
@Tklengyel My Mentor,
@Dos98 Me :P



About Me
--------
My name is Ayush Dosaj. I am a CSE Undergraduate student pursuing my bachelors at VIT Vellore, India. I am very interested in C/Cpp Software Development and Malware Analysis. GSoC 2019 was the best opportunity to contribute to this field and start a new journey.


Project goal
------------

    - Implement Process Injector for Linux Operating System


The project
-----------

### Approach

To inject a new process into Linux, we need to hijack the execution flow of the target process and execute Exec function present in Libc shared library of the process with the right setup of stack and registers.

### Diagrammatic Representation

![Injector code flow](images/linux_injector_transparent.png)


### Injection of a New Process in Detail

Injection of a new process into Linux os :

    - Registering a callback for CR3 register & wait for the target process callback.
    - On finding the target process, Setting up a breakpoint and register a callback for INT3 instruction (Process is in kernel mode) and removing a the CR3 trap.
    - Extracting user mode RIP from RCX/Stack when the process is in kernel mode
    - Setting up a breakpoint at this RIP and register a callback for INT3 instruction and removing the last INT3 trap.
    - Every time When the target process scheduled to run in user mode 
        - Finding the symbol address of exec present in Libc shared library present in process memory to inject the process
        - Setting RIP to exec symbol address.
        - Setting up registers and user stack for the new process to execute
    - Process Injection can pass/fail.
    - To determine the result of the injection, We set up a new CR3 breakpoint to find the injected process on success by checking the Name and Pid on CR3 callback. Whereas On Failure, RAX register returns -1.
    - Finally removing both breakpoints at the end.



### ShellCode injection

Currently Shelllcode injection implementation need some dubugging. 

Current implementation:

    - Load shellcode file to memory 
    - Registering a callback for CR3 register & wait for the target process callback.
    - On finding the target process, Setting up a breakpoint and register a callback for INT3 instruction (Process is in kernel mode) and removing a the CR3 trap.
    - Extracting user mode RIP from RCX/Stack when the process is in kernel mode
    - Setting up a breakpoint at this RIP and register a callback for INT3 instruction and removing the last INT3 trap.
    - Whenever the target process scheduled to run in user mode 
        - Finding the symbol address of exec present in Libc shared library present in process memory to inject the process
        - Setting RIP to malloc symbol address.
        - Setting up registers and user stack for the new process to execute
    - When it returns, it contains the payload address for the memory allocated.
    - Again we set up the stack and registers for memset to execute. It fills memory with a constant byte
    - Now we write shellcode to memory allocated and set RIP to a memory address.
    - It should execute the shellcode, but it ends with Seg Fault in the running process. It needs some debugging to correct this behavior.


I will create a seperate blog for shellcode injection and execution when it is completed.

How to Run the Injector
----------------------- 

Process Injection

    Using Injector:
        Target pid: 1234
        Process to Inject: ls
        Arguments: -l

        - `./src/injector -r /root/linux.json -d 1 -i 1234 -e ls -m execproc -f -l`

    Using Drakvuf:
        Target pid: 1234
        Process to Inject: gnome-calculator
        Activate plugin: cpuidmon

        - `./src/drakvuf -r /root/linux.json -d 1 -i 1234 -e gnome-calculator -m execproc -a cpuidmon`

Future Work
-----------

### Make Injector Fork + Exec

Currently, Exec is used to inject a new process into the system. It replaces the target process with a new process image in the new address space. 

Whereas, a better approach is Fork + Exec. Forking a child process and injecting a new process into that process so that target does not stop executing.

### Shellcode Injection (In dev)

After Process Injection, I started working on Shellcode injection. Shellcode injection implementation needs some correction as it ends up in segmentation fault after injection.


### ELF64 Relocated Symbols

Symbols defined in ".rela section" of Elf64 in memory, mapping of these relocated symbols to their offset is left. I was able to find these symbols offset but not their mapping to their symbol name as symbol names are not present in memory. This small portion is a Todo.



Learning experience
-------------------

Time flies, GSoC 2019 has come to an end. 

During the early weeks of GSoC, I tried to understand the code base more thoroughly. Every day I used to spend time learning about Linux and Virtualization and OS concepts in depth that are required. 
This project was a challenge for me. I was new to virtualization concepts. I feel that GSoC project allowed me to gain practical application of the theories and models that I would not have acquired in a more traditional classroom environment.

Whole summer, I spent time learning, implementing, and debugging stuff. It is an entirely different experience developing a feature for such a complex project. In the beginning, I used to think I won't be able to it. With consistent dedication and guidance of my mentor @tklengyel, I was able to solve all the issues and complete this project.

Drakvuf project is very rich in the technology stack; I learned memory forensics, Rekall, Volatility, GDB, Linux kernel, RE, and hands-on experience in C/Cpp software development. Also, I became familiar with opensource projects like Xen hypervisor and LibVMI. 

This GSoC experience with Project drakvuf was unimaginable for a Bachelor. All other contributors were Masters or Ph.D. students. They also helped me understand and debug stuff. Thanks to Ajinkya Rajput for keeping me going whenever I am stuck.


Credits
-------

Thanks....(todo)