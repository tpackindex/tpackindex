# Index-lite
A crude simulator used to illustrate the performance of TPacks based system with ski-rental merging policy  

**Why is it 'lite' and it what ways is it 'crude'?**  
We need to emulate making billions of updates to millions of terms and processing millions of queries.  
Faithfully representing the state of the system w.r.t every term would make it an order of magnitude slower, even two.  
Indeed, we have a more precise model that feeds on 120GB of inputs and runs for a couple of hours. 

Hoewever, since we know that the terms are following an inherently skewed distribution, we apply the following tricks:
+ instead of handling millions of terms we handle merely dozens of term-packs. 
+ we make the relaxing assumption that every term in the pack behaves just like the other terms.  
I.e., for a pack with 850 terms that gets one million updates and half 3,000 queries per time slice we assume that those are distributed uniformly and each term gets 1,000,000 / 850 updates and 3,000 / 850 queries.  
+ It works surprisingly well, since we assign the terms into packs such that they are comparable w.r.t. update and query frequency.  
+ In practice, this results in a few groups with a handful of hevy hitters and many groups with literally millions of singletons.
+ The same principle is applied to mamanging term cache -- we just emulate having millions of queries hitting the cache by doing weighted round-robin on the term-packs, thus making sure that the frequent terms hit the cache more often than the infrequent. 
+ Obviously, when the simulator has to read two segments from disk and write their union as new segment we are only recording this operation, rather than actually transferring bits on a hard-drive. 

**Build**  
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
