# Index-lite
A crude simulator used to illustrate the performance of TPacks based system with ski-rental merging policy  

Build with standard cmake:
```bash
cmake . && make
```

Expects a single argument -- query rate. A natural number >= 1, denoting the amount of queries per 1 million updates.  
For small values (e.g., <64) finishes in seconds.    
For larger values (e.g, >1000) may take a minute or two.  

There is really no point to run with more queries than it takes to make ALW better than log. 
