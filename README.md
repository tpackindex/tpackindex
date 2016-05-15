# Index-lite
A crude simulator used to illustrate the performance of TPacks based system with ski-rental merging policy  

Build with standard cmake:
```bash
cmake . && make
```

Expects a single argument -- query rate. A natural number >= 1, denoting the amount of queries per 1 million updates.  
For small values (e.g., <64) finishes in seconds.  
For larger values (e.g., >1000) may take a minute or two.  

There is really no point to run with more queries than it takes to make ALW better than log. 

**Report breakdown**
```
SkiBsdMerge HD 96--90 Evictions:   219 Total-seen-postings: 62184295597 Total-queries: 1024000 Query-reads: r-posts: 9605502953447 r-seeks: 12440492 Consolidation: r-posts: 387037484488 r-seeks: 25024 w-posts: 449221780085 w-seeks: 12804 Total-query-minutes: 5522.74 Total-merge-minutes: 358.867 Sum-All: 5881.61
```
+ SkiBsdMerge &mdash; algorithm label (AlwaysMeger and LogrthMerge are the other two possible)  
+ HD  &mdash; which hardware we are emulating (HD/SSD)
+ 96--90  &mdash; the left number is the UB share of memory (in percents), the right is the eviction limit (in percents)
+ Evictions  &mdash; how ofthen the UB became full (grows when UB share smaller and eviction limit bigger)
+ Total-seen-postings   &mdash; actually the ones we evicted to disk
+ Query-reads (r-posts,r-seeks); Consolidation (r-posts,r-seeks,w-posts,w-seeks) &mdash; brekdown of disk I/O
+ Total-query-minutes,Total-merge-minutes &mdash; same as above but in minutes
+ Sum-all &mdash; the sum of Total-query-minutes,Total-merge-minutes
