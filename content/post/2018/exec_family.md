---
title: "Exec family - A quick note"
date: 2018-11-11T16:40:25+05:30
draft: false
categories:
  - C
  - GNU/Linux
  - Systems
slug: "exec family"
---

This is just for personal future reference:

There is a family of exec() library functions, all of which have slightly different characteristics:

```c
int execl ( const char *path, const char *arg, ... );
int execlp( const char *file, const char *arg, ... );
int execle( const char *path, const char *arg, ..., char *const envp[] );
int execv ( const char *path, char *const argv[] );
int execvp( const char *file, char *const argv[] );
int execvpe(const char *file, char *const argv[], char *const envp[]);
```
- **if `p` in the name:** accepts a program name and it searches for the program in `$PATH` env var
- **if `l` or `v` in the name:** the program name must be given in full(path).
- **if `e` in the name:** function accepts additional array of env vars. 

**Usage Notes:**
```
execl("/bin/echo","echo", "Hello World", NULL);
```
When a program is invoked from the shell, the shell sets the first element of the argument list argv[0]) to the name of the program, the second element of the argument list (argv [1]) to the first command-line argument, and so on. When you use an exec function in your programs, **you, too, should pass the name of the function as the first element of the argument list.**

This is the reason we are passing `"echo"` as the second argument in `execl()`

Additional information:

All of these are user space frontends to `execve()` system call. 
```
int execve( const char *filename, char *const argv[], char *const envp[] );
```
`execve()` executes the program pointed to by filename.  This causes the program that is currently being run by the calling process to be replaced with a new program, with newly initialized stack, heap, and (initialized and uninitialized) data segments.

- see `man 2 execve` for the systemcall
- see `man 3 execve` for info on exec family
- see `man 3 exec` for library function info on exec family
- see `man 1 exec` for info on the exec shell utility (which i did not cover in this blog, but just added for fun)


More links:
https://ece.uwaterloo.ca/~dwharder/icsrts/Tutorials/fork_exec/
http://www.makelinux.net/alp/ (Chapter 3)
