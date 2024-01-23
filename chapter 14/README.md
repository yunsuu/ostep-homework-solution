# Memory API

# Q1

First, write a simple program called `null.c` that creates a pointer to an integer, sets it to `NULL`, and then tries to dereference it. Compile this into an executable called `null`. What happens when you run this program?

Segmentation fault 에러가 발생한다.

```c
// Online C compiler to run C program online
#include <stdio.h>

int main() {
    // Write C code here
    int *n = NULL;
    printf("%d", *n);
    return 0;
}
```

# Q2

Next,compile this program with symbol information included(with the `-g` flag). Doing so let’s put more information into the executable, enabling the debugger to access more useful information about variable names and the like. Run the program under the debugger by typing `gdb null` and then, once `gdb` is running, typing `run`. What does `gdb` show you?

### result

```
Program received signal SIGSEGV, Segmentation fault.
0x000055555540065e in main () at hello.c:7
```

| Signal | Value | Action | Comment |
| --- | --- | --- | --- |
| SIGSEGV | 11 | Core | Invalid memory reference |

# Q3

Finally, use the `valgrind` tool on this program. We’ll use the `memcheck` tool that is a part of `valgrind` to analyze what happens. Run this by typing in the following: `valgrind --leak-check=yes null`. What happens when you run this? Can you interpret the output from the tool?

### command

```
valgrind --leak-check=yes ./null
```

### result

```
==1808== Memcheck, a memory error detector
==1808== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==1808== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==1808== Command: ./hello
==1808==
==1808== Invalid read of size 4
==1808==    at 0x10865E: main (hello.c:7)
==1808==  Address 0x0 is not stack'd, malloc'd or (recently) free'd
==1808==
==1808==
==1808== Process terminating with default action of signal 11 (SIGSEGV): dumping core
==1808==  Access not within mapped region at address 0x0
==1808==    at 0x10865E: main (hello.c:7)
==1808==  If you believe this happened as a result of a stack
==1808==  overflow in your program's main thread (unlikely but
==1808==  possible), you can try to increase the size of the
==1808==  main thread stack using the --main-stacksize= flag.
==1808==  The main thread stack size used in this run was 8388608.
==1808==
==1808== HEAP SUMMARY:
==1808==     in use at exit: 0 bytes in 0 blocks
==1808==   total heap usage: 0 allocs, 0 frees, 0 bytes allocated
==1808==
==1808== All heap blocks were freed -- no leaks are possible
==1808==
==1808== For counts of detected and suppressed errors, rerun with: -v
==1808== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
Segmentation fault (core dumped)

```

# Q4

Write a simple program that allocates memory using `malloc()` but forgets to free it before exiting. What happens when this program runs? Can you use `gdb` to find any problems with it? How about `valgrind` (again with the `--leak-check=yes` flag)?

### code

```c
//q4.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int size = 10;  // Set the size of the array to 10

    // Dynamically allocate an array
    int *dynamicArray = (int *)malloc(size * sizeof(int));

    // Check if allocation was successful
    if (dynamicArray == NULL) {
        fprintf(stderr, "Memory allocation failed.\n");
        return 1;  // Exit on failure
    }

    // Initialize the array with values (example: 0, 2, 4, ...)
    for (int i = 0; i < size; i++) {
        dynamicArray[i] = i;
    }

    // Print the values in the array
    printf("The array contains the following integers:\n");
    for (int i = 0; i < size; i++) {
        printf("%d ", dynamicArray[i]);
    }

    // Free the allocated memory
    //free(dynamicArray);

    return 0;
}
```

### command

```
valgrind --leak-check=yes ./q4
```

### reuslt

```
==2321== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==2321== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==2321== Command: ./hello
==2321==
The array contains the following integers:
0 1 2 3 4 5 6 7 8 9 ==2321==
==2321== HEAP SUMMARY:
==2321==     in use at exit: 0 bytes in 0 blocks
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Memcheck, a memory error detectorvalgrind --leak-check=yes ./hello
==2345== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==2345== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==2345== Command: ./hello
==2345==
The array contains the following integers:
0 1 2 3 4 5 6 7 8 9 ==2345==
==2345== HEAP SUMMARY:
==2345==     in use at exit: 40 bytes in 1 blocks
==2345==   total heap usage: 2 allocs, 1 frees, 1,064 bytes allocated
==2345==
==2345== 40 bytes in 1 blocks are definitely lost in loss record 1 of 1
==2345==    at 0x4C31B0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==2345==    by 0x108779: main (hello.c:8)
==2345==
==2345== LEAK SUMMARY:
==2345==    definitely lost: 40 bytes in 1 blocks
==2345==    indirectly lost: 0 bytes in 0 blocks
==2345==      possibly lost: 0 bytes in 0 blocks
==2345==    still reachable: 0 bytes in 0 blocks
==2345==         suppressed: 0 bytes in 0 blocks
==2345==
==2345== For counts of detected and suppressed errors, rerun with: -v
==2345== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

# Q5

[Single Bell DB](https://www.notion.so/14ed4a366000411a80004576633d9414?pvs=21)

Write a program that creates an array of integers called `data` of size 100 using `malloc`; then, set `data[100]` to zero. What happens when you run this program? What happens when you run this program using `valgrind`? Is the program correct?

### code

```c
//q5.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int size = 100;  // Set the size of the array to 10

    // Dynamically allocate an array
    int *dynamicArray = (int *)malloc(size * sizeof(int));

    // Check if allocation was successful
    if (dynamicArray == NULL) {
        fprintf(stderr, "Memory allocation failed.\n");
        return 1;  // Exit on failure
    }

    // Initialize the array with values (example: 0, 2, 4, ...)
    for (int i = 0; i < size; i++) {
        dynamicArray[i] = 0;
    }

    // Print the values in the array
    printf("The array contains the following integers:\n");
    for (int i = 0; i < size; i++) {
        printf("%d ", dynamicArray[i]);
    }

    // Free the allocated memory
    free(dynamicArray);

    return 0;
}
```

### command

```
valgrind --leak-check=yes ./q5
```

### reuslt

```
==2539== Memcheck, a memory error detector
==2539== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==2539== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==2539== Command: ./hello
==2539==
The array contains the following integers:
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
==2539== All heap blocks were freed -- no leaks are possibleallocated 0 0 0 0 0 0 0 0 0 0 0 0 0
==2539== All heap blocks were freed -- no leaks are possibleallocated 0 0 0 0 0 0 0 0 0 0 0 0 0
==2539== All heap blocks were freed -- no leaks are possibleallocated 0 0 0 0 0 0 0 0 0 0 0 0 0
==2539== All heap blocks were freed -- no leaks are possibleallocated 0 0 0 0 0 0 0 0 0 0 0 0 0
==2539== All heap blocks were freed -- no leaks are possibleallocated 0 0 0 0 0 0 0 0 0 0 0 0 0
==2539== All heap blocks were freed -- no leaks are possibleallocated 0 0 0 0 0 0 0 0 0 0 0 0 0
==2539==
==2539== For counts of detected and suppressed errors, rerun with: -v
==2539== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

# Q6

Create a program that allocates an array of integers (as above), frees them, and then tries to print the value of one of the elements of the array. Does the program run? What happens when you use `valgrind` on it?

실행은 정상적으로 되는데 `valgrind`를 이용한 에러검출도 가능하다

### code

```c
//q6.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int size = 100;  // Set the size of the array to 10

    // Dynamically allocate an array
    int *dynamicArray = (int *)malloc(size * sizeof(int));

    // Check if allocation was successful
    if (dynamicArray == NULL) {
        fprintf(stderr, "Memory allocation failed.\n");
        return 1;  // Exit on failure
    }

    // Initialize the array with values (example: 0, 2, 4, ...)
    for (int i = 0; i < size; i++) {
        dynamicArray[i] = 0;
    }

		**// Not Appropriate free 
    //free(dynamicArray);**

		printf("%d ", dynamicArray[5]);
		
		**// Appropriate free 
    free(dynamicArray);**

    return 0;
}
```

### command

```
valgrind --leak-check=yes ./q6
```

### reuslt

```
valgrind --leak-check=yes ./hello
==2951== Memcheck, a memory error detector
==2951== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==2951== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==2951== Command: ./hello
==2951==
==2951== Invalid read of size 4
==2951==    at 0x1087EE: main (hello.c:25)
==2951==  Address 0x522f054 is 20 bytes inside a block of size 400 free'd
==2951==    at 0x4C32D3B: free (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==2951==    by 0x1087E5: main (hello.c:23)
==2951==  Block was alloc'd at
==2951==    at 0x4C31B0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==2951==    by 0x108779: main (hello.c:9)
==2951==
5 ==2951==
==2951== HEAP SUMMARY:
==2951==     in use at exit: 0 bytes in 0 blocks
==2951==   total heap usage: 2 allocs, 2 frees, 1,424 bytes allocated
==2951==
==2951== All heap blocks were freed -- no leaks are possible
==2951==
==2951== For counts of detected and suppressed errors, rerun with: -v
==2951== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

# Q7

Now pass a funny value to free (e.g., a pointer in the middle of the array you allocated above). What happens? Do you need tools to find this type of problem?

### code

```c
//q6.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int size = 100;  // Set the size of the array to 10

    // Dynamically allocate an array
    int *dynamicArray = (int *)malloc(size * sizeof(int));

    // Check if allocation was successful
    if (dynamicArray == NULL) {
        fprintf(stderr, "Memory allocation failed.\n");
        return 1;  // Exit on failure
    }

    // Initialize the array with values (example: 0, 2, 4, ...)
    for (int i = 0; i < size; i++) {
        dynamicArray[i] = i;
    }
    
    dynamicArray = &dynamicArray[50];
    printf("%d ", dynamicArray[5]);

    // Free the allocated memory
    free(dynamicArray);

    return 0;
}
```

### command

```
valgrind --leak-check=yes ./q6
```

### reuslt

```
//when excute 

free(): invalid pointer
Aborted

$ valgrind --leak-check=yes ./hello

==3257== Memcheck, a memory error detector
==3257== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==3257== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==3257== Command: ./hello
==3257==
==3257== Invalid free() / delete / delete[] / realloc()
==3257==    at 0x4C32D3B: free (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==3257==    by 0x10880A: main (hello.c:26)
==3257==  Address 0x522f108 is 200 bytes inside a block of size 400 alloc'd
==3257==    at 0x4C31B0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==3257==    by 0x108779: main (hello.c:9)
==3257==
55 ==3257==
==3257== HEAP SUMMARY:
==3257==     in use at exit: 400 bytes in 1 blocks
==3257==   total heap usage: 2 allocs, 2 frees, 1,424 bytes allocated
==3257==
==3257== 400 bytes in 1 blocks are definitely lost in loss record 1 of 1
==3257==    at 0x4C31B0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==3257==    by 0x108779: main (hello.c:9)
==3257==
==3257== LEAK SUMMARY:
==3257==    definitely lost: 400 bytes in 1 blocks
==3257==    indirectly lost: 0 bytes in 0 blocks
==3257==      possibly lost: 0 bytes in 0 blocks
==3257==    still reachable: 0 bytes in 0 blocks
==3257==         suppressed: 0 bytes in 0 blocks
==3257==
==3257== For counts of detected and suppressed errors, rerun with: -v
==3257== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
```

# Q8

Try out some of the other interfaces to memory allocation. For example, create a simple vector-like data structure and related routines that use `realloc()` to manage the vector. Use an array to store the vectors elements; when a user adds an entry to the vector, use `realloc()` to allocate more space for it. How well does such a vector perform? How does it compare to a linked list? Use `valgrind` to help you find bugs.

# Q9

Spend more time and read about using `gdb` and `valgrind`. Knowing your tools is critical; spend the time and learn how to become an expert debugger in the UNIX and C environment.
