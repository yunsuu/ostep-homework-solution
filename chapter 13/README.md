# **The Abstraction: Address Spaces**

# Q1

1. The first Linux tool you should check out is the very simple tool `free`. First, type `man free` and read its entire manual page; it’s short, don’t worry!

# Q2

1. The first Linux tool you should check out is the very simple tool `free`. First, type `man free` and read its entire manual page; it’s short, don’t worry!

### answer

직관적으로 

### command

```
free -m
```

### result

```
total        used        free      shared  buff/cache   available
Mem:          15611        5738        2832         181        7040        9340
Swap:        8191         106        8085
```

# Q2

Now, run `free`, perhaps using some of the arguments that might be useful (e.g., `-m`, to display memory totals in megabytes). How much memory is in your system? How much is free? Do these numbers match your intuition?

직관적으로 맞지 않는다. free 메모리가 감소하는 것은 직관적으로 예상을 했지만, free의 값이 0이 되지 않는다. 아마 예상해보자면 각 프로세스마다 차지할 수 있는 max size가 존재할 것으로 예상하고 그것을 넘으면 프로세스를 kill 하는 것 같다. 

### command

```
free -m
```

### result

```
total        used        free      shared  buff/cache   available
Mem:          15611        5738        2832         181        7040        9340
Swap:        8191         106        8085
```

# Q3

Next, create a little program that uses a certain amount of memory, called `memory-user.c`. This program should take one command-line argument: the number of megabytes of memory it will use. When run, it should allocate an array, and constantly stream through the array, touching each entry. The program should do this indefinitely, or, perhaps, for a certain amount of time also specified at the command line.

`memory-user.c code`

```c
//memory-user.c code
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    unsigned long long mb = 1024 * 1024; // 1MB
    while (1) {
        void *ptr = malloc(mb);
        if (ptr == NULL) {
            printf("Memory allocation failed: Program terminated\n");
            break;
        }
        printf("Memory allocation success\n");

        sleep(1);
    }

    return 0;
}
```

# Q4

Now, while running your `memory-user` program, also (in a different terminal window, but on the same machine) run the `free` tool. How do the memory usage totals change when your program is running? How about when you kill the `memory-user` program? Do the numbers match your expectations? Try this for different amounts of memory usage. What happens when you use really large amounts of memory?

점점 증가하다가 kill이 된 후 실행하기 전의 값과 비슷한 레벨로 돌아간다.

# Q5

Let’s try one more tool, known as `pmap`. Spend some time, and read the `pmap` manual page in detail.

# Q6

To use `pmap`, you have to know the **process ID** of the process you’re interested in. Thus, first run `ps auxw` to see a list of all processes; then, pick an interesting one, such as a browser. You can also use your `memory-user` program in this case (indeed, you can even have that program call `getpid()` and print out its PID for your convenience).

메모리 점유율이 2.7 정도 이상으로 올라가지 않는다. kill 되기 직전에도 메모리 점유율이 100에 가까워 지지 않는다. 오히려 CPU 계산 점유율이 더 높다

# Q7

Now run `pmap` on some of these processes, using various flags (like `X`) to reveal many details about the process. What do you see? How many different entities make up a modern address space, as opposed to our simple conception of code/stack/heap?

### command

```bash
$pmap 2199
```

### result

```bash

2199:   ./a
0000559170600000      4K r-x-- a
0000559170800000      4K r---- a
0000559170801000      4K rw--- a
0000559172571000 10384692916K rw---   [ anon ]
00007988a2b12000 6711148544K rw---   [ anon ]
00007fc8b2b12000   1948K r-x-- libc-2.27.so
00007fc8b2cf9000   2048K ----- libc-2.27.so
00007fc8b2ef9000     16K r---- libc-2.27.so
00007fc8b2efd000      8K rw--- libc-2.27.so
00007fc8b2eff000     16K rw---   [ anon ]
00007fc8b2f03000    156K r-x-- ld-2.27.so
00007fc8b3107000      8K rw---   [ anon ]
00007fc8b312a000      4K r---- ld-2.27.so
00007fc8b312b000      4K rw--- ld-2.27.so
00007fc8b312c000      4K rw---   [ anon ]
00007ffda8704000    132K rw---   [ stack ]
00007ffda87f0000     16K r----   [ anon ]
00007ffda87f4000      8K r-x--   [ anon ]
ffffffffff600000      4K --x--   [ anon ]
```

### command

```bash
$pmap 2382 -X
```

### result

```bash
$pmap 2382 -X

2382:   ./a
         Address Perm   Offset Device  Inode        Size    Rss    Pss Referenced Anonymous LazyFree ShmemPmdMapped FilePmdMapped Shared_Hugetlb Private_Hugetlb  Swap SwapPss Locked THPeligible Mapping
    5643c5a00000 r-xp 00000000 2b:441 393430           4      4      4          4         0        0              0             0              0               0     0       0      0           0 a
    5643c5c00000 r--p 00000000 2b:441 393430           4      4      4          4         4        0              0             0              0               0     0       0      0           0 a
    5643c5c01000 rw-p 00001000 2b:441 393430           4      0      0          0         0        0              0             0              0               0     4       4      0           0 a
    5643c5e7a000 rw-p 00000000  00:00      0  6050919452 236364 236364     236364    236364        0              0             0              0               0     4       4      0           0 [heap]
    78be8c0b6000 rw-p 00000000  00:00      0  6711148544 250344 250344     250344    250344        0              0             0              0               0 11800   11800      0           0
    7efe9c0b6000 r-xp 00000000  00:c5     64        1948   1000      6       1000         0        0              0             0              0               0     0       0      0           0 libc-2.27.so
    7efe9c29d000 ---p 001e7000  00:c5     64        2048      0      0          0         0        0              0             0              0               0     0       0      0           0 libc-2.27.so
    7efe9c49d000 r--p 001e7000  00:c5     64          16      4      4          4         4        0              0             0              0               0    12      12      0           0 libc-2.27.so
    7efe9c4a1000 rw-p 001eb000  00:c5     64           8      8      8          8         8        0              0             0              0               0     0       0      0           0 libc-2.27.so
    7efe9c4a3000 rw-p 00000000  00:00      0          16     12     12         12        12        0              0             0              0               0     0       0      0           0
    7efe9c4a7000 r-xp 00000000  00:c5     37         156    156      0        156         0        0              0             0              0               0     0       0      0           0 ld-2.27.so
    7efe9c6ab000 rw-p 00000000  00:00      0           8      4      4          4         4        0              0             0              0               0     4       4      0           0
    7efe9c6ce000 r--p 00027000  00:c5     37           4      4      4          4         4        0              0             0              0               0     0       0      0           0 ld-2.27.so
    7efe9c6cf000 rw-p 00028000  00:c5     37           4      0      0          0         0        0              0             0              0               0     4       4      0           0 ld-2.27.so
    7efe9c6d0000 rw-p 00000000  00:00      0           4      0      0          0         0        0              0             0              0               0     4       4      0           0
    7ffddfa33000 rw-p 00000000  00:00      0         132      8      8          8         8        0              0             0              0               0     4       4      0           0 [stack]
    7ffddfb6e000 r--p 00000000  00:00      0          16      0      0          0         0        0              0             0              0               0     0       0      0           0 [vvar]
    7ffddfb72000 r-xp 00000000  00:00      0           8      4      0          4         0        0              0             0              0               0     0       0      0           0 [vdso]
ffffffffff600000 --xp 00000000  00:00      0           4      0      0          0         0        0              0             0              0               0     0       0      0           0 [vsyscall]
                                             =========== ====== ====== ========== ========= ======== ============== ============= ============== =============== ===== ======= ====== ===========
                                             12761150780 489628 488474     489628    488464        0              0             0              0               0 10088   10088      0           0 KB
```

# Q8

Finally,let’s run pmap on your `memory-user` program, with different amounts of used memory. What do you see here? Does the output from pmap match your expectations?
