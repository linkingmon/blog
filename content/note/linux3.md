---
title: "Linux Kernel - Part 3"
date: 2026-08-29
tags: ["linux", "mmap"]
---

In this post I would like to review the function for using `mmap` that is introduced in [Why Linux Has This Syscall?! - Tsoding](https://www.youtube.com/watch?v=sFYFuBzu9Ow)

His repo can be found on [Github](https://github.com/tsoding/mmap), where he demos 4 type of usage: 

(1) allocating memroy
(2) reading files
(3) IPC (inter-processing cache)
(4) Precise control over memory protection

## Allocating Memory
```c
#include <assert.h>

#include <sys/mman.h>

#define N 10

int main(int argc, char *argv[])
{
    int *xs = mmap(NULL, N * sizeof(int),
                   PROT_READ | PROT_WRITE,
                   MAP_PRIVATE | MAP_ANONYMOUS,
                   0, 0);
    assert(xs != MAP_FAILED);

    assert(N >= 2);
    xs[0] = 0;
    xs[1] = 1;

    for (int i = 2; i < N; ++i) {
        xs[i] = xs[i - 1] + xs[i - 2];
    }

    int err = munmap(xs, N * sizeof(int));
    assert(err >= 0);

    return 0;
}
```

The above code request `N` int size memory from linux kernel through `mmap`, and return the virtual memory in the end by `munmap`.

## Reading Files
```c
#include <assert.h>
#include <fcntl.h>
#include <sys/mman.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
    assert(argc >= 2);
    const char *filepath = argv[1];

    int fd = open(filepath, O_RDWR);
    assert(fd >= 0);

    struct stat statbuf;
    int err = fstat(fd, &statbuf);
    assert(err >= 0);

    char *ptr = mmap(NULL, statbuf.st_size,
                     PROT_READ | PROT_WRITE,
                     MAP_PRIVATE,
                     // MAP_SHARED,
                     fd, 0);
    assert(ptr != MAP_FAILED);
    close(fd);

    for (size_t i = 0; i < statbuf.st_size / 2; ++i) {
        int j = statbuf.st_size - i - 1;
        int t = ptr[i];
        ptr[i] = ptr[j];
        ptr[j] = t;
    }

    ssize_t n = write(1, ptr, statbuf.st_size);
    assert(n == statbuf.st_size);

    err = munmap(ptr, statbuf.st_size);
    assert(err >= 0);

    return 0;
}
```
It ulitize the `struct stat` and `fstat` to read a file and its size. `mmap` tries to create the file-backed page, and we select `MAP_PRIVATE` so that when doing write it will do copy-on-write to form a private RAM page. In the end the private RAM is write back to the file. 


## IPC (inter-processing cache)
```c
#include <assert.h>
#include <stdio.h>

#include <sys/mman.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

#define N 10

int *xs = 0;

void print_xs(void)
{
    assert(N >= 1);
    printf("%d", xs[0]);
    for (int i = 1; i < N; ++i) {
        printf(" %d", xs[i]);
    }
    printf("\n");
}

int main(int argc, char *argv[])
{
    xs = mmap(NULL, N * sizeof(int),
              PROT_READ | PROT_WRITE,
              MAP_SHARED | MAP_ANONYMOUS,
              0, 0);
    assert(xs != MAP_FAILED);

    for (int i = 0; i < N; ++i) {
        xs[i] = i + 1;
    }

    pid_t child_pid = fork();
    assert(child_pid != -1);

    if (child_pid == 0) {
        // child
        for (int i = 0; i < N / 2; ++i) {
            int t = xs[i];
            xs[i] = xs[N - i - 1];
            xs[N - i - 1] = t;
        }
        printf("Child:\t");
        print_xs();
    } else {
        // parent
        waitpid(child_pid, NULL, 0);
        printf("Parent:\t");
        print_xs();
    }

    return 0;
}
```
In this case the `fork` coppied the vitual memory mapping to child and the memory is `MAP_SHARED` so that when the child changed that RAM page, the parent are also able to see these changes.


## Precise control over memory protection
```c
#include <assert.h>
#include <stdio.h>

#include <fcntl.h>
#include <sys/mman.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
    assert(argc >= 2);
    const char *filepath = argv[1];

    int fd = open(filepath, O_RDWR);
    assert(fd >= 0);

    struct stat statbuf;
    int err = fstat(fd, &statbuf);
    assert(err >= 0);

    void *ptr = mmap(NULL, statbuf.st_size,
                     PROT_EXEC,
                     MAP_PRIVATE,
                     fd, 0);
    assert(ptr != MAP_FAILED);
    close(fd);

    ((void (*)(void))ptr)();

    printf("We are back!\n");

    return 0;
}
```
The mapping is defined as `PROT_EXEC` and in `((void (*)(void))ptr)();` we tries to cast to function pointer and call it.
