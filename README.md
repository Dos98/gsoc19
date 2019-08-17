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
My name is Ayush Dosaj. I am a CSE Undergraduate student pursuing my bachelors at VIT Vellore, India. I am very interested in C/Cpp Software Development and Malware Analysis. GSoC 2019 is the best opportunity to contribute to this field and start a new journey.


Project goal
------------

    - Implement Process Injector for Linux Operating System


The project
-----------

### Approach

To inject a new process into Linux, we need to hijack the execution flow of the target process and execute Exec function present in Libc shared library of the process with the right setup of stack and registers.

### Diagramatic Reprsentation



### Injection of a New Process in Detail

Injection of a new process into Linux os :

    - Registering a callback for CR3 register to find the target process into the system
    - Setting up a breakpoint and register a callback for INT3 instruction (Process in kernel Mode)
    - Extracting RIP in user mode from RCX/Stack when the process in kernel mode
    - Setting up a breakpoint at this RIP and register a callback for INT3 instruction
    - Finding the symbol address present in libc in process memory to inject the process
    - Setting up registers and stack for process injection
    - Process Injection can pass/fail.
    - To find the result of the injection, setting up a new CR3 breakpoint to find the injected process

### ShellCode injection



How to Run the Injector
-----------------------


Future Work
-----------

### Make Injector Fork + Exec

Currently, Exec is used to inject a new process into the system. It replaces the target process with a new process image in the new address space. 

Whereas, a better approach would be Fork + Exec. Forking a child process and injecting a new process into that process so that target does not stop executing.

### Shellcode Injection (In dev)

After Process Injection, I started working on Shellcode injection. Shellcode injection implementation needs some correction as it ends up in segmentation fault after injection.


### ELF64 Relocated Symbols

Symbols defined in ".rela section" of Elf64 in memory, mapping of these relocated symbols to their offset is left. I was able to find these symbols offset but not their mapping to their symbol name as symbol names are not present in memory. This small portion is a Todo.



Learning experience
-------------------

Sadly, GSOC 2019 has come to an end. 

I spent my summer ........



Credits
-------

