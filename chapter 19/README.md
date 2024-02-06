## tlb.c

```c
#include <unistd.h>
#include <sys/time.h>
#include <stdio.h>
#include <pthread.h>

void* thread_entry(void* dummy) {

    int NUMPAGES = 1;
    int N = 1000000;
    
    int MAXPAGES = 10000;
    
    while(1){
    if(NUMPAGES > MAXPAGES) break; 
    
    int PAGESIZE = sysconf(_SC_PAGESIZE); //bytes
    int jump = PAGESIZE / sizeof(int); // 총 한 페이지 안에 몇개의 int가 들어가있는 지수
    // printf("%d", jump);
    int a[jump*NUMPAGES];
    int cnt = 0;
    
     struct timeval start, end;
    gettimeofday(&start, NULL);
    
    for(int j=0; j<N; j++){
        cnt = 0;
        for(int i=0; i< jump*NUMPAGES; i+=jump){
            a[i] += 1;
            cnt+=1;
        }
    }
    
     gettimeofday(&end, NULL);
    
    // 경과 시간 계산
    long seconds = end.tv_sec - start.tv_sec;
    long microseconds = end.tv_usec - start.tv_usec;
    double elapsed = seconds + microseconds / 1e6 ;
    double time = elapsed / cnt;

    // 결과 출력
    printf("[%d] The elapsed time of program execution: %f sec\n",NUMPAGES, time);
    NUMPAGES = NUMPAGES*2;
    sleep(1);
    }

    return 0;
}

int main() {
    pthread_t thread;
    int num = 1;
    
    pthread_create(&thread, NULL, thread_entry, (void*)&num);
    pthread_join(thread, NULL);
    
    return 0;
}
```

### reuslt

```c
[1] The elapsed time of program execution: 0.004538 sec
[2] The elapsed time of program execution: 0.003952 sec
[4] The elapsed time of program execution: 0.005048 sec
[8] The elapsed time of program execution: 0.007340 sec
[16] The elapsed time of program execution: 0.018558 sec
[32] The elapsed time of program execution: 0.021626 sec
[64] The elapsed time of program execution: 0.026644 sec
[128] The elapsed time of program execution: 0.025845 sec
[256] The elapsed time of program execution: 0.022258 sec
[512] The elapsed time of program execution: 0.022458 sec
[1024] The elapsed time of program execution: 0.024906 sec
```

# Q1

For timing, you’ll need to use a timer (e.g., `gettimeofday()`). How precise is such a timer? How long does an operation have to take in order for you to time it precisely? (this will help determine how many times, in a loop, you’ll have to repeat a page access in order to time it successfully)

`CLOCK_PROCESS_CPUTIME_ID` has resolution of 1 nanosecond on Linux, 1000 nanoseconds on macOS.

# Q2

Write the program, called `tlb.c`, that can roughly measure the cost of accessing each page. Inputs to the program should be: the number of pages to touch and the number of trials.

# Q3

Now write a script in your favorite scripting language (bash?) to run this program, while varying the number of pages accessed from 1 up to a few thousand, perhaps incrementing by a factor of two per iteration. Run the script on different machines and gather some data. How many trials are needed to get reliable measurements?

100000 trials.

# Q4

Next, graph the results, making a graph that looks similar to the one above. Use a good tool like `ploticus` or even `zplot`. Visualization usually makes the data much easier to digest; why do you think that is?

# Q5

One thing to watch out for is compiler optimization. Compilers do all sorts of clever things, including removing loops which increment values that no other part of the program subsequently uses. How can you ensure the compiler does not remove the main loop above from your TLB size estimator?

Using gcc's optimize option `gcc -O0` to disable optimization. This is the default setting.

# Q6

Another thing to watch out for is the fact that most systems today ship with multiple CPUs, and each CPU, of course, has its own TLB hierarchy. To really get good measurements, you have to run your code on just one CPU, instead of letting the scheduler bounce it from one CPU to the next. How can you do that? (hint: look up “pinning a thread” on Google for some clues) What will happen if you don’t do this, and the code moves from one CPU to the other?

Use `sched_setaffinity(2)`, `pthread_setaffinity_np(3)`, `taskset(1)` or `sudo systemd-run -p AllowedCPUs=0 ./tlb.out` on Linux, `cpuset_setaffinity(2)` or `cpuset(1)` on FreeBSD.

Or use `hwloc-bind package:0.pu:0 -- ./tlb.out`.

# Q7

Another issue that might arise relates to initialization. If you don’t initialize the array `a` above before accessing it, the first time you access it will be very expensive, due to initial access costs such as demand zeroing. Will this affect your code and its timing? What can you do to counterbalance these potential costs?

Use `calloc(3)` to initialize array then measure time.
