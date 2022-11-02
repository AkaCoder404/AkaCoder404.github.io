---
layout: post
title: "What the fork?"
categories:
    - Notes
    - Operating Systems
tags:
    - cpp
date: '2022-11-02 00:00:00 +0000'
--- 

This article focuses on calculating the total number of proccesses in a code that uses fork(). But first lets describe fork briefly.

In an operating system, particularly in the context of the Unix operating system, the term **fork**, or the function `fork`, is an operation where a process creates a copy of itself in order to start the execution of a different process. This copy, known as "child process", then calls the `exec` system call to overlay itself with the other program, basically ending execution of former program in favor of the other. The fork operation creates a **seperate** address space for the child, but the child has an exact copy of all the memory segments of the parent process. In short, fork allows user procceses to start other proccess while running. 

## How to Fork?
The fork function is quite simple, here is a "Hello World" of forking. 
```cpp
int main(void) {
    pid_t pid = fork();

    if (pid < 0) {                                 // error if forked process returns pid < 0
        perror("fork failed");                     // no new processes is created
        exit(EXIT_FAILURE);
    }
    // fork is successful, both procceses are executing in main, and must branch based on pid
    else if (pid == 0) {                           // child fork returns pid of 0, handle child process
        printf("Hello from the child process!\n"); // can exec other program here
        _exit(EXIT_SUCCESS);
    }
    else {                                          // parent process returns pid > 0
        int status;
        printf("Hello from parent process");
        (void)waitpid(pid, &status, 0);
    }
    return EXIT_SUCCESS;
}
```
## Calculating how many forks?
These are related problems to calculating exactly how many child processes are created. All of the problems below can use these following libraries to run.

```cpp
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
```

Using `g++ -o file.o file.cpp`, we can run the code locally.

### Problem 1
How many times is hello world printed?
```cpp
int main () {
    fork();
    fork();
    print("Hello World");
}
```
Twice!

### Problem 2
How many child processes are created by fork? Try drawing the relationship between the proccesses.
```cpp
#define LOOP 3

int main() {
    int pid;
    int i;
    for (i = 0; i < LOOP; i++) {
        pid = fork();
        if (pid < 0) {
            printf("Error occured\n");
        }
        else if (pid == 0) {                // handle child proccesses
            printf("Child Process i=%d, pid=%d, parent pid=%d\n", i, getpid(), getppid());
        } 
        else {
            wait(NULL);
        }
    }
}
```
Since fork is called `LOOP` number of times, there is a total of `2^LOOP` proccesses, and `2^LOOP -1`  child proccesses. The output of the program (the pid's will be different every run), is 

```console
Child Process i=0, pid=13916, parent pid=13915
Child Process i=1, pid=13917, parent pid=13916
Child Process i=2, pid=13918, parent pid=13917
Child Process i=2, pid=13919, parent pid=13916
Child Process i=1, pid=13920, parent pid=13915
Child Process i=2, pid=13921, parent pid=13920
Child Process i=2, pid=13922, parent pid=13915
```

The child creation "tree" can be described below,

![](../../assets/img/posts/fork1.png)


The child creation process can be broken down as such, where the P0 represents the parent process, and with each loop, each child calls fork and creates its own child proccess. As we can see, there a total of `2^LOOP -1` child proccesses

![](../../assets/img/posts/fork2.png)

Wait, why add `wait(NULL)`?  This is because the parent process is finished by the time the child asks for its parents pid. When a process is finished, all its children are reassigned as children of the initial proccess, which has a pid of 1. Thus, we must use [`wait()`](https://linux.die.net/man/2/wait) in the paren'ts code to wait for the child to execute. If we didn't use it, we would have gotten something like this

```console
Child Process i=0, pid=13546, parent pid=13545
Child Process i=1, pid=13547, parent pid=13545
Child Process i=1, pid=13549, parent pid=13546
Child Process i=2, pid=13551, parent pid=1
Child Process i=2, pid=13552, parent pid=1
Child Process i=2, pid=13550, parent pid=13547
Child Process i=2, pid=13548, parent pid=1
``` 
Which was not what we expected to see.

### Problem 3
How many child process are created by fork?
```cpp
int main() {  
    int i = 1;
    if (fork()) {
        i++;
    }
    else if (fork()) {
        i--;
    }
    else {
        i++;
    }   
}
```
Answer in the comments!

## Related Sources
