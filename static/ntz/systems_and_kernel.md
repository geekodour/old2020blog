#### Notes from System Programming YouTube series
[Link to playlist](https://www.youtube.com/playlist?list=PLPXsMt57rLtjNzxZBDg9xJB7KT83WStBO)

**Part I**
### Context switch
- Kernelmode and usermode
    - Mode switch
        - Context switch
            - save state of p1 and switch to p2
            - **Running** state vs **Ready** state
            - **Blocked** state (waiting for some signal etc)
            - A **blocked** process cannot immediately become running, it has to be **ready** first
- Ways to create process:
    - Unix/Linux:  `fork()`: clones p1 to p2, p2 runs the program
    - process ids or pids are 16-bit numbers

- Creating processes in C
Windows API provides the `spawn` family, Linux does not provide in one step.
Instead it gives `fork()` and the `exec()` family of functions.
    - `system()` library call
    - `fork()` and `exec()` system call
        - `fork()` returns twice
            - once in the parent and once in the child
        - `exec()` exec functions replace the program running in a process with another program. 
            - exec family has various functions:


#### Others
- ELF(Executable and Linkable Format)
