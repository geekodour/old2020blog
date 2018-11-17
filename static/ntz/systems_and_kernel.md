#### Notes from System Programming YouTube series
- [Link to playlist](https://www.youtube.com/playlist?list=PLPXsMt57rLtjNzxZBDg9xJB7KT83WStBO)
- [Advanced Linux Programming Book](http://www.makelinux.net/alp/)

**Part I**
### Context switch
- Kernelmode and usermode
    - Process context info is stored in PCB
    - Mode switch
        - Context switch
            - save state of p1 and switch to p2
            - **Running** state vs **Ready** state
            - **Blocked** state (waiting for some signal etc)
            - A **blocked** process cannot immediately become running, it has to be **ready** first
    - The dispatch mechanism(for context switch)
        - a user process running means the dispacher isnt because cpu only run one thing at a time
        - ways os gains control
            - exceptions
                - sys calls, page faults, seg faults etc
            - hardware intrrrupts
                - keyboard, network, ISR(Interrupt service routine)

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
    - `wait()` family: four different system calls
- **Signals**
    - they are async
    - when a process receives a signal, it processes the signal immediately, without finishing the current function or even the current line of code.

### Threads
- each thread executes a thread function
- `pthread_create` creates a new thread
    - `pthread_create  (&thread_id,  NULL, &print_xs, NULL);`
    - Returns immediately
    - Linux schedules both threads asynchronously, and your program must not rely on the relative order in which instructions are executed in the two threads.
- `pthread_exit` exits a process takes in retval(The thread return value, like the thread argument, is of type void* )
- `pthread_join` is like the wait family
-  **Thread Attributes(`pthread_attr_t`)**:
      - `detach state`
        - a thread may be created as a **joinable thread** (the default) or as a **detached thread**
        - a detached thread, in contrast, is cleaned up automatically when it terminates.
        - `pthread_attr_setdetachstate()`
- Thread termination
  - Usually thread ends after finishing execution or by calling `pthread_exit`
  - **Thread cancellation**: when a thread requests that another thread should terminate (`pthread_cancel`)
    - async cancellation : immediate
      - `pthread_setcanceltype`
    - sync cancellation : only after the thread decides that it can be cancelled at this point, requests are queued
      - cancellation points
        - create the cancellation point using:
        - `pthread_testcancel()` : process a pending cancellation in a synchronously cancelable thread
      - default
      - aka deferred
    - uncancellable
      - `pthread_setcancelstate` with args `PTHREAD_CANCEL_DISABLE` or `PTHREAD_CANCEL_ENABLE`
      - implements **critical sections** : A critical section is a sequence of code that must be executed either in its entirety or not at all

- Thread specific data
  - each thread has its own call stack
  - **thread-specific data area**: sometimes it is desirable to duplicate a certain variable so that each thread has a separate copy
    - variables stored in this area are duplicated for each thread
    - special functions for accessing **thread specific data areas**
    - create as many of type `void *`
    - done using `pthread_key_create` and related fuctions

- Synchronization and Critical Sections
Three main synchronization device in GNU/Linux: Mutex, Semaphores and Condition Variables
  - **Bugs(as the writer says):**
  - Race conditions: the threads are racing one another to change the same data structure.
    - Solutions:
      - **Mutexes**: (MUTual EXclusion locks) : a mutex is a special lock that only one thread may lock at a time.
        - GNU/Linux guarantees that race conditions do not occur among threads attempting to lock a mutex
        - Use pthered_mutexes inside thread functions
  - Deadlocks: a deadlock occurs when one or more threads are stuck waiting for something that never will occur.
    - can happen due to mutex
  - **Some use cases:**
  - Semaphores: A semaphore is a counter(+ve) that can be used to synchronize multiple threads.
    - As with a mutex, GNU/Linux guarantees that checking or modifying the value of a semaphore can be done safely, without creating a race condition.
    - Two operations: `wait` and `post`: `sem_wait()` and `sem_post()`
      - A non-blocking `sem_trywait()` is also there
    - A semaphore is represented by a `sem_t` variable, initilize it with `sem_init()`, use `sem_destroy()` to desctroy the semaphore
    - Types:
      - Kernel semaphore
      - Posix semaphore
      - SystemV semaphore or Process semaphores
    - Can be used as a shared counter
    - Random tip: If you don't want a function to exit just use `while(1)` like a event loop lol
  - Condition Variables: with it, you can implement more complex conditions under which threads execute.
    - A condition variable enables you to implement a condition under which a thread executes and, inversely, the condition under which the thread is blocked.
      - can use semaphores and condition variable together
      - condition variable has no counter or memory unlike semaphores
    - `pthread_cond_t` : Each condition variable should be accompanied by a mutex
    - `pthread_cond_signal(pthread_cond_t)`: signals unblocking, i.e if thread is blocked by a CV, it will be unblocked. otherwise ignored
    - `pthread_cond_wait(pthread_cond_t, pthread_mutex_t)`: signals blocking of a thread



#### Others
- ELF(Executable and Linkable Format)
