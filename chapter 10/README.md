# Multi processor  scheduler

If you wnat some more detail, check this URL and read README

https://github.com/remzi-arpacidusseau/ostep-homework/tree/master/cpu-sched-multi

# Q1
To start things off, let’s learn how to use the simulator to study how to build an effective multi-processor scheduler. The first simulation will run just one job, which has a run-time of 30, and a working-set size of 200. Run this job (called job ’a’ here) on one simulated CPU as follows: ./multi.py -n 1 -L a:30:200. How long will it take to complete? Turn on the -c flag to see a final answer, and the -t flag to see a tick-by-tick trace of the job and how it is scheduled.

### command

```
python multi.py -n 1 -L a:30:200 -c -t
```

### result

```
ARG seed 0
ARG job_num 3
ARG max_run 100
ARG max_wset 200
ARG job_list a:30:200
ARG affinity
ARG per_cpu_queues False
ARG num_cpus 1
ARG quantum 10
ARG peek_interval 30
ARG warmup_time 10
ARG cache_size 100
ARG random_order False
ARG trace False
ARG trace_time False
ARG trace_cache False
ARG trace_sched False
ARG compute True

Job name:a run_time:30 working_set_size:200

Scheduler central queue: ['a']

	 0   a
   1   a
   2   a
   3   a
   4   a
   5   a
   6   a
   7   a
   8   a
   9   a
----------
  10   a
  11   a
  12   a
  13   a
  14   a
  15   a
  16   a
  17   a
  18   a
  19   a
----------
  20   a
  21   a
  22   a
  23   a
  24   a
  25   a
  26   a
  27   a
  28   a
  29   a

Finished time 30
```

# Q2

Now increase the cache size so as to make the job’s working set (size=200) fit into the cache (which, by default, is size=100); for example, run ./multi.py -n 1 -L a:30:200 -M 300. Can you predict how fast the job will run once it fits in cache? (hint: remember the key parameter of the warm rate, which is set by the -r flag) Check your answer by running with the solve flag (-c) enabled.

### answer

warm_rate is 10. This means that after running for 10 seconds, all data is cached in the CPU, and subsequent tasks are executed more efficiently using the cache.

The command execution result is 20 seconds.

10 + 20/10 = 20

When all tasks are placed in the cache, you can see that the remaining execution time is halved.

This simulation shows that the time to access memory differs from the time to access the cache by a factor of 2.

warm_rate가 10이다. 이 의미는 10초를 실행하면 CPU에 캐시가 모두 저장되어 그 이후 작업은 캐시를 이용해 더 효율적으로 실행된다. 

커맨드 실행 결과는 20초

10+20/10 = 20

캐시에 모두 작업이 들어가면 남은 실행시간의 시간이 절반의 시간으로 줄어든다는 걸 알 수 있다. 

이 시뮬레이션은 Memory에 엑세스하는 시간이 캐시에 엑세스 하는 시간과 2배가 차이 난다는 걸 알 수 있다. 

### command

```
python multi.py -n 1 -L a:30:200 -M 300 -c -t
```

### result

```
ARG seed 0
ARG job_num 3
ARG max_run 100
ARG max_wset 200
ARG job_list a:30:200
ARG affinity
ARG per_cpu_queues False
ARG num_cpus 1
ARG quantum 10
ARG peek_interval 30
ARG warmup_time 10
ARG cache_size 300
ARG random_order False
ARG trace True
ARG trace_time False
ARG trace_cache False
ARG trace_sched False
ARG compute True

Job name:a run_time:30 working_set_size:200

Scheduler central queue: ['a']

   0   a
   1   a
   2   a
   3   a
   4   a
   5   a
   6   a
   7   a
   8   a
   9   a
----------
  10   a
  11   a
  12   a
  13   a
  14   a
  15   a
  16   a
  17   a
  18   a
  19   a

Finished time 20

Per-CPU stats
  CPU 0  utilization 100.00 [ warm 50.00 ]
```

# Q3

One cool thing about `multi.py` is that you can see more detail about what is going on with different tracing flags. Run the same simulation as above, but this time with time left tracing enabled (`-T`). This flag shows both the job that was scheduled on a CPU at each time step, as well as how much run-time that job has left after each tick has run. What do you notice about how that second column decreases?

### answer

As answered in Q2, you can see that the remaining scheduling time is reduced by a factor of 2 after warm_rate seconds.

Q2에서 대답했듯이 warm_rate 만큼의 시간 이후에 남은 스캐줄링 시간이 2배로 줄어드는 걸 볼 수 있다.

### command

```
python multi.py -n 1 -L a:30:200 -c -t -M 300 -T
```

### result

```
Job name:a run_time:30 working_set_size:200

Scheduler central queue: ['a']

   0   a [ 29]
   1   a [ 28]
   2   a [ 27]
   3   a [ 26]
   4   a [ 25]
   5   a [ 24]
   6   a [ 23]
   7   a [ 22]
   8   a [ 21]
   9   a [ 20]
----------------
  10   a [ 18]
  11   a [ 16]
  12   a [ 14]
  13   a [ 12]
  14   a [ 10]
  15   a [  8]
  16   a [  6]
  17   a [  4]
  18   a [  2]
  19   a [  0]

Finished time 20

Per-CPU stats
  CPU 0  utilization 100.00 [ warm 50.00 ]
```

# Q4

Now add one more bit of tracing, to show the status of each CPU cache for each job, with the `-C` flag. For each job, each cache will either show a blank space (if the cache is cold for that job) or a ’w’ (if the cache is warm for that job). At what point does the cache become warm for job ’a’ in this simple example? What happens as you change the warmup time parameter (`-w`) to lower or higher values than the default?

### command

```
python multi.py -n 1 -L a:30:200 -c -t -M 300 -T -C -w 10
python multi.py -n 1 -L a:30:200 -c -t -M 300 -T -C -w 5
```

### result

```

//first result
Finished time 20

Per-CPU stats
  CPU 0  utilization 100.00 [ warm 50.00 ]

//second result
Finished time 18

Per-CPU stats
  CPU 0  utilization 100.00 [ warm 72.22 ]
```

# Q5

At this point, you should have a good idea of how the simulator works for a single job running on a single CPU. But hey, isn’t this a multi-processor CPU scheduling chapter? Oh yeah! So let’s start working with multiple jobs. Specifically, let’s run the following three jobs on a two-CPU system (i.e., type `./multi.py -n 2 -L a:100:100,b:100:50,c:100:50`) Can you predict how long this will take, given a round-robin centralized scheduler? Use `-c` to see if you were right, and then dive down into details with `-t` to see a step-by-step and then `-C` to see whether caches got warmed effectively for these jobs. What do you notice?

### answer

Queue [a,b,c]

CPU0 [a,c,b,a,c,b…]

CPU1 [b,a,c,b,a,c…]

It seems like you've gained some insights from your analysis:

- When a process's data loading into the cache matches the time-slice, scheduling tends to work efficiently.
- Processes with large working sets are better allocated to separate CPUs for efficiency, while smaller tasks can be grouped for scheduling. If CPU0 had only 'a,' its performance would have doubled, and if CPU1 had only 'a,' it would have shown a higher cache hit rate, leading to increased efficiency.
- If you only consider the process turnaround time, you can optimize scheduling by setting the time-slice to a multiple of the time it takes for the process's data to load into the cache, thereby increasing cache-friendliness.

These insights highlight the importance of understanding cache behavior and data access patterns in scheduling and performance optimization.

Queue [a,b,c]

CPU0 [a,c,b,a,c,b…]

CPU1 [b,a,c,b,a,c…]

이런식으로 예상했다 스캐줄링 된 스캐줄을 보면 캐시 친화성으로 인한 효율성은 얻지 못한 것이라 생각해서 total_runtime / cpu_number 의 식으로 계산이 될 것이라 생각해 300/2 = 150으로 예상했다. 정답이였다

warm이 0인걸 보아 캐시 히트가 전혀 일어나지 않음을 확인할 수 있다.

이 과정을 통해 알아낸 것 다음과 같다.

- 프로세스의 데이터가 캐시에 load되는 시간과 time-slice이 일치하면 스캐줄링이 깔끔하게 떨어진다.
- 작업 사이즈가 큰 프로세스는 따로 CPU를 할당하는 것이 더 효율적이고 작업이 작은 프로세스들은 모아서 스캐줄링 하는 것이 효율적이다. CPU0에 a만 없었다면 속도가 2배 빨라졌을 것이고 CPU1에 온전히 a만 있다면 a도 높은 캐시 친화율을 보여줘 효율성이 올라갈 것이다.
- 오로지 프로세스 반환시간만 생각한다면 프로세스의 데이터가 캐시에 load되는 시간의 배수만큼 time-slice의 값이 결정된다면 캐시 친화성이 더욱 높아지도록 스케줄링을 짤 수 있을 것이다.

### result

```
Finished time 150

Per-CPU stats
  CPU 0  utilization 100.00 [ warm 0.00 ]
  CPU 1  utilization 100.00 [ warm 0.00 ]
```

# Q6

### answer

expected Q5

### command

```
python multi.py -n 2 -L a:100:100,b:100:50,c:100:50 -A a:0,b:1,c:1 -c -C -t -T
```

### result

```
Finished time 110

Per-CPU stats
  CPU 0  utilization 50.00 [ warm 40.91 ]
  CPU 1  utilization 100.00 [ warm 81.82 ]
```

# Q7

One interesting aspect of caching multiprocessors is the opportunity for better-than-expected speed up of jobs when using multiple CPUs (and their caches) as compared to running jobs on a single processor. Specifically, when you run on *N* CPUs, sometimes you can speed up by more than a facor of *N*, a situation entitled **super-linear speedup**. To experiment with this, use the job description here (`-L a:100:100,b:100:100,c:100:100`) with a small cache (`-M 50`) to create three jobs. Run this on systems with 1, 2, and 3 CPUs (`-n 1, -n 2, -n 3`). Now, do the same, but with a larger per-CPU cache of size 100. What do you notice about performance as the number of CPUs scales? Use `-c` to confirm your guesses, and other tracing flags to dive even deeper.

### answer

Regardless of cache hitting, as the CPU size increases, the processing time decreases, and if the cache affinity is high, it can decrease even more.

However, in reality, there are operations that can only be processed by a single CPU (such as I.O interrupt operations). Therefore, in reality, increasing the CPU does not necessarily reduce processing time in proportion to the number of CPUs.

캐시 히팅 유무 상관없이 CPU크기가 늘어날 수록 작업시간이 줄어들고 캐시 친화도가 높으면 그보다 더 줄어들기도 한다.

하지만 현실에서는 하나의 CPU에서밖에 처리하지 못하는 연산들이 있다. (예를들어 I.O 인터럽트 연산) 그래서 현실에서는 CPU가 늘어난다고 무작정 CPU수에 비례해서  작업시간이 줄어들지 않는다.

### command

```
python multi.py -n 1 -L a:100:100,b:100:100,c:100:100 -M 50 -c -t -T -C    // 300 no cache
python multi.py -n 2 -L a:100:100,b:100:100,c:100:100 -M 50 -c -t -T -C    // 150 no cache
python multi.py -n 3 -L a:100:100,b:100:100,c:100:100 -M 50 -c -t -T -C    // 100 no cache

python multi.py -n 1 -L a:100:100,b:100:100,c:100:100 -M 100 -c -t -T -C   // 300 no cache
python multi.py -n 2 -L a:100:100,b:100:100,c:100:100 -M 100 -c -t -T -C   // 150 no cache
python multi.py -n 3 -L a:100:100,b:100:100,c:100:100 -M 100 -c -t -T -C   // 55, 10 + 90/2
```

# Q8

One other aspect of the simulator worth studying is the per-CPU scheduling option, the `-p` flag. Run with two CPUs again, and this three job configuration (`-L a:100:100,b:100:50,c:100:50`). How does this option do, as opposed to the hand-controlled affinity limits you put in place above? How does performance change as you alter the ’peek interval’ (`-P`) to lower or higher values? How does this per-CPU approach work as the number of CPUs scales?

### answer

"peek_interval" is a parameter that controls how often the scheduling of tasks assigned to individual CPUs checks the scheduling queue of other CPUs to fetch tasks. Therefore, a lower value for this parameter leads to better performance. However, in real operating systems, the task of checking each queue also consumes resources, so checking too frequently may actually result in reduced efficiency.

As the number of CPUs increases, you can observe that performance improves for a certain period but then levels off.

Having a large number of CPUs doesn't necessarily lead to better performance.

"peek_interval"은 이러한 개별 CPU에 할당된 작업 스케줄링을 얼마나 자주 다른 CPU의 스케줄 큐를 확인하여 작업을 가져올지를 조절하는 매개 변수이다. 때문에 이 값이 낮을수록 성능이 더 좋아진다. 하지만 실제 운영체제에서는 각 큐를 확인하는 작업 또한 리소스이기 때문에 너무 자주검사하게 되면 효과가 오히려 떨어질 수 있다.

CPU수가 늘어날 수록 성능은 일정기간 향상되다가 정체되는 것을 볼 수 있다.

무작정 CPU가 많아도 안좋나 보다.

### command

```
$ python multi.py -n 2 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C    // 100 = 60 + 10 + (100 - 30 - 10)/2
$ python multi.py -n 2 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C -P 10 // 100
$ python multi.py -n 2 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C -P 40 // 115
$ python multi.py -n 3 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C    // 55
$ python multi.py -n 4 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C    // 60
$ python multi.py -n 5 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C    // 55
$ python multi.py -n 8 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C    // 60
$ python multi.py -n 9 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C    // 60
$ python multi.py -n 10 -L a:100:100,b:100:100,c:100:100 -p -c -t -T -C    // 60
```

# Q9

Finally, feel free to just generate random workloads and see if you can predict their performance on different numbers of processors, cache sizes, and scheduling options. If you do this, you’ll soon be a **multi-processor scheduling master**, which is a pretty awesome thing to be. Good luck!
