# **Scheduling: Proportional Share**

If you wnat some more detail, check this URL and read README

https://github.com/remzi-arpacidusseau/ostep-homework/tree/master/cpu-sched-lottery

## Q1

Compute the solutions for simulations with 3 jobs and random seeds of 1, 2, and 3.

### command

```
$ python lottery.py -s 1 -j 3 -c
$ python lottery.py -s 2 -j 3 -c
$ python lottery.py -s 3 -j 3 -c
```

### result

```
$python lottery.py -s 1 -j 3

ARG jlist
ARG jobs 3
ARG maxlen 10
ARG maxticket 100
ARG quantum 1
ARG seed 1

Here is the job list, with the run time of each job:
  Job 0 ( length = 1, tickets = 84 )
  Job 1 ( length = 7, tickets = 25 )
  Job 2 ( length = 4, tickets = 44 )

Here is the set of random numbers you will need (at most):
Random 651593
Random 788724
Random 93859
Random 28347
Random 835765
Random 432767
...

Jobs:
 (* job:0 timeleft:5 tix:94 )  (  job:1 timeleft:7 tix:73 )  (  job:2 timeleft:3 tix:30 )
Random 544177 -> Winning ticket 63 (of 197) -> Run 0
  Jobs:
 (* job:0 timeleft:4 tix:94 )  (  job:1 timeleft:7 tix:73 )  (  job:2 timeleft:3 tix:30 )
Random 444854 -> Winning ticket 28 (of 197) -> Run 0
  Jobs:
 (* job:0 timeleft:3 tix:94 )  (  job:1 timeleft:7 tix:73 )  (  job:2 timeleft:3 tix:30 )
Random 268241 -> Winning ticket 124 (of 197) -> Run 1
  Jobs:
 (  job:0 timeleft:2 tix:94 )  (* job:1 timeleft:7 tix:73 )  (  job:2 timeleft:3 tix:30 )
Random 35924 -> Winning ticket 70 (of 197) -> Run 0
  Jobs:
 (* job:0 timeleft:2 tix:94 )  (  job:1 timeleft:6 tix:73 )  (  job:2 timeleft:3 tix:30 )
Random 27444 -> Winning ticket 61 (of 197) -> Run 0
  Jobs:
 (* job:0 timeleft:1 tix:94 )  (  job:1 timeleft:6 tix:73 )  (  job:2 timeleft:3 tix:30 )
--> JOB 0 DONE at time 14
Random 464894 -> Winning ticket 55 (of 103) -> Run 1
  Jobs:
 (  job:0 timeleft:0 tix:--- )  (* job:1 timeleft:6 tix:73 )  (  job:2 timeleft:3 tix:30 )
Random 318465 -> Winning ticket 92 (of 103) -> Run 2
  Jobs:
 (  job:0 timeleft:0 tix:--- )  (  job:1 timeleft:5 tix:73 )  (* job:2 timeleft:3 tix:30 )
Random 380015 -> Winning ticket 48 (of 103) -> Run 1
  Jobs:
 (  job:0 timeleft:0 tix:--- )  (* job:1 timeleft:5 tix:73 )  (  job:2 timeleft:2 tix:30 )
Random 891790 -> Winning ticket 16 (of 103) -> Run 1
...
```

## Q2

Now run with two specific jobs: each of length 10, but one (job 0) with just 1 ticket and the other (job 1) with 100 (e.g., `-l 10:1,10:100`). What happens when the number of tickets is so imbalanced? Will job 0 ever run before job 1 completes? How often? In general, what does such a ticket imbalance do to the behavior of lottery scheduling?

### **English Answer**

The process starvation state leads to a drastic increase in the response time for processes with fewer lottery tickets. From the results, we can see that job 0 starts executing only after the completion of job 1, which has a higher number of lottery tickets. This indicates an unfair distribution of process allocation.

### **Korean Answer**

프로세스의 기아 상태가 일어나서 추첨권이 적은 프로세스의 응답시간이 극단적으로 늘어나게 된다. 해당 결과에서는 추첨권이 많은 job 1의 실행시간이 완전히 끝난 후에야 job 0이 실행되는 것을 볼 수 있다. 이는 프로세스 배분이 공정하게 이루어지지 않음을 나타낸다.

### command

```
$ python lottery.py -l 10:1,10:100 -c
```

### result

```
python lottery.py -l 10:1,10:100 -c
ARG jlist 10:1,10:100
ARG jobs 3
ARG maxlen 10
ARG maxticket 100
ARG quantum 1
ARG seed 0

Here is the job list, with the run time of each job:
  Job 0 ( length = 10, tickets = 1 )
  Job 1 ( length = 10, tickets = 100 )

** Solutions **
Random 844422 -> Winning ticket 62 (of 101) -> Run 1
  Jobs:
 (  job:0 timeleft:10 tix:1 )  (* job:1 timeleft:10 tix:100 )
Random 757955 -> Winning ticket 51 (of 101) -> Run 1
  Jobs:
 (  job:0 timeleft:10 tix:1 )  (* job:1 timeleft:9 tix:100 )
Random 420572 -> Winning ticket 8 (of 101) -> Run 1
  Jobs:
.
.
.
  Jobs:
 (  job:0 timeleft:10 tix:1 )  (* job:1 timeleft:1 tix:100 )
--> JOB 1 DONE at time 10
Random 908113 -> Winning ticket 0 (of 1) -> Run 0
(* job:0 timeleft:10 tix:1 )  (  job:1 timeleft:0 tix:--- )
Random 504687 -> Winning ticket 0 (of 1) -> Run 0
  Jobs:
.
.
.
```

## Q3

 When running with two jobs of length 100 and equal ticket allocations of 100 (`-l 100:100,100:100`), how unfair is the scheduler? Run with some different random seeds to determine the (probabilistic) answer; let unfairness be determined by how much earlier one job finishes than the other.

### **English Answer**

While there is some margin of error, the process is mostly fair.

### **Korean Answer**

약간의 오차가 있지만 거의 공평하다.

### command

```
$ python lottery.py -l 10:1,10:100 -c
```

## Q4

How does your answer to the previous question change as the quantum size (`-q`) gets larger?

### English Answer

As q decreases, the fairness of the process increases. However, as it gets smaller, frequent context switching can occur, leading to unnecessary overhead and performance degradation. Therefore, it is important to find an appropriate q value considering context switching.

### Korean Answer

q가 적어질 수록 프로세스의 공평성은 올라간다. 하지만 작아질수록 context switching이 자주 발생함으로 불필요한 오버헤드로 성능저하가 일어날 수 있다. 때문에 context switching을 고려해 적절한 q값을 찾는게 중요하다

### command

```
$ python [lottery.py](http://lottery.py/) -l 10:10,10:10 -c -q 1 -s 103
```
