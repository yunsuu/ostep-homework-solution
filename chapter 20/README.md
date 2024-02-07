# Before read

This post is about the practical exercises of 'Operating Systems: Principles and Practice.' Please read the linked article and check out the posting

https://github.com/remzi-arpacidusseau/ostep-homework/tree/master/vm-smalltables

# Q1

With a linear page table, you need a single register to locate the page table, assuming that hardware does the lookup upon a TLB miss. How many registers do you need to locate a two-level page table? A three-level table?

하나만 필요하다 왜냐하면 TLB는 VPN↔PFN(최종 데이터가 있는 PFN)의 쌍 정보를 저장해 두는 역할이고 이를 저장해 둠으로써 주소 변환을 하지 않아도 되게 하는 역할을 수행한다. 그래서 아무리 레벨이 높은 페이지더라도 TLB하나로도 충분히 동작한다.

# Q2

Use the simulator to perform translations given random seeds 0, 1, and 2, and check your answers using the `-c` flag. How many memory references are needed to perform each lookup?

# Q3

Given your understanding of how cache memory works, how do you think memory references to the page table will behave in the cache? Will they lead to lots of cache hits (and thus fast accesses?) Or lots of misses (and thus slow accesses)?

질문의 의도를 잘 이해 못하겠다. 당연히 캐시가 있으면 잘 더 잘 동작할 수 밖에 없지 않은가?
