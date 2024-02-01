# Before read

This post is about the practical exercises of 'Operating Systems: Principles and Practice.' Please read the linked article and check out the posting

https://github.com/remzi-arpacidusseau/ostep-homework/tree/master/vm-freespace

# Q1

First run with the flags `-n 10 -H 0 -p BEST -s 0` to generate a few random allocations and frees. Can you predict what alloc()/free() will return? Can you guess the state of the free list after each request? What do you notice about the free list over time?

- 시간이 지남에 따라 빈 공강 리스트가 점점 많아진다. 많아질 수록 할당하기가 복잡해진다. 명령어 후반부에 사이즈를 크게 할당하게 된다면. 
모두 할당이 free된, 빈 공간의 메모리라고 하더라도 빈공간 리스트의 파편화 때문에 아무것도 할당하지 못할 가능성이 생긴다.

### command

```
python malloc.py -n 10 -H 0 -p BEST -s 0
```

### result

```
seed 0
size 100
baseAddr 1000
headerSize 0
alignment -1
policy BEST
listOrder ADDRSORT
coalesce False
numOps 10
range 10
percentAlloc 50
allocList
compute True

ptr[0] = Alloc(3) returned 1000 (searched 1 elements)
Free List [ Size 1 ]: [ addr:1003 sz:97 ]

Free(ptr[0])
returned 0
Free List [ Size 2 ]: [ addr:1000 sz:3 ][ addr:1003 sz:97 ]

ptr[1] = Alloc(5) returned 1003 (searched 2 elements)
Free List [ Size 2 ]: [ addr:1000 sz:3 ][ addr:1008 sz:92 ]

Free(ptr[1])
returned 0
Free List [ Size 3 ]: [ addr:1000 sz:3 ][ addr:1003 sz:5 ][ addr:1008 sz:92 ]

ptr[2] = Alloc(8) returned 1008 (searched 3 elements)
Free List [ Size 3 ]: [ addr:1000 sz:3 ][ addr:1003 sz:5 ][ addr:1016 sz:84 ]

Free(ptr[2])
returned 0
Free List [ Size 4 ]: [ addr:1000 sz:3 ][ addr:1003 sz:5 ][ addr:1008 sz:8 ][ addr:1016 sz:84 ]

ptr[3] = Alloc(8) returned 1008 (searched 4 elements)
Free List [ Size 3 ]: [ addr:1000 sz:3 ][ addr:1003 sz:5 ][ addr:1016 sz:84 ]

Free(ptr[3])
returned 0
Free List [ Size 4 ]: [ addr:1000 sz:3 ][ addr:1003 sz:5 ][ addr:1008 sz:8 ][ addr:1016 sz:84 ]
```

# Q2

How are the results different when using a WORST fit policy to search the freelist(`-p WORST`)? What changes?

- BEST과 비교했을때 빈 공간 리스트의 파편화가 더욱 심해진다. BEST 정책과 비교했을때 더더욱 메모리 할당이 어려워지게된다

### command

```
python malloc.py -n 10 -H 0 -p WORST -s 0
```

# Q3

What about when using FIRST fit `(-p FIRST`)? What speeds up when you use first fit?

- BEST정책은 빈공간 리스트를 전부 탐색해야 하지만 FIRST 정책은 빈 공간 리스트의 전부 탐색하지 않아도 되기 때문에 속도가 향상된다.

# Q4

For the above questions, how the list is kept ordered can affect the time it takes to find a free location for some of the policies. Use the different free list orderings(`-l ADDRSORT,-l SIZESORT+,-l SIZESORT-`) to see how the policies and the list orderings interact.

- `SIZESORT-`가 가장 많은 파편화가 일어나는 것을 볼 수 있다. 이는 결과값의 Free List Size 수로 확인해볼 수 있다. 당연하다 사이즈 기준으로 내림차순으로 정렬하게 되면 가장 큰 청크부터 나누게 되니 시간이 지나면 지날수록 더 많은 청크가 생길 수 밖에 없다.
- 하지만 BEST 정책은 배열을 모두 탐색한 후 할당할 곳을 정하기 때문에 BEST 정책을 사용할때는 빈 공간 리스트를 어떻게 정렬하던 같은 파편화 정도가 나온다

### command

```
python malloc.py -S 10 -n 100 -H 0 -p FIRST -s 0 -c -l SIZESORT-
python malloc.py -S 10 -n 100 -H 0 -p FIRST -s 0 -c -l SIZESORT+
python malloc.py -S 10 -n 100 -H 0 -p FIRST -s 0 -c -l ADDRSORT

python malloc.py -S 10 -n 100 -H 0 -p BEST -s 0 -c -l SIZESORT-
python malloc.py -S 10 -n 100 -H 0 -p BEST -s 0 -c -l SIZESORT+
```

# Q5

Coalescing of a free list can be quite important. Increase the number of random allocations (say to -n 1000). What happens to larger allocation requests over time? Run with and without coalescing (i.e., without and with the -C flag). What differences in outcome do you see? How big is the free list over time in each case? Does the ordering of the list matter in this case?

빈공간 리스트 병합기능이 없는 명령어는 수행이 전부 완료되는데 5초 정도 소요되었고,

병합기능이 있는 명령어는 2초정도  수행되었다 약 2배정도 성능 개선이 되는 것을 확인할 수 있었다.

- 주소 기준으로 리스트를 정렬하는 것이 병합에 효율적이다

### command

```
//take 5 sec
python malloc.py -n 1000 -H 0 -p BEST -s 100 -c 

//take 2 sec
python malloc.py -n 1000 -H 0 -p BEST -s 100 -c -C
```

# Q6

What happens when you change the percent allocated fraction `-P` to higher than 50? What happens to allocations as it nears 100? What about as the percent nears 0?

- 할당비율이 늘어날수록 당연하게 메모리 공간이 적어진다

# Q7

What kind of specific requests can you make to generate a highly-fragmented free space? Use the -A flag to create fragmented free lists, and see how different policies and options change the organization of the free list.

- 헤드의 길이가 길수록, `alignment`의 단위가 클수록 internal fragmentation이 더 많이 일어날 것이다.
