# Scalability
As system grows ( in terms of data volume, complexity or traffic) there should be a reasonable way of dealing with growth

## Measure the current load of the system

Load parameters for different levels,
1. Web server : request per second
2. Batch processing : throughput
3. DB : Ratio of reads vs writes
4. cache : hit ratio
5. chat room : number of active users
6. API : success rate

Example : twitter ( X)
post a tweet : Write heavy 
RPS
Average : 5k/s
Peak : 12k/s

Home timeline  : Read heavy
RPS
300k requests/ s

### Investigate what happens when load increases
1. Increase load and keep resources same ( CPU, memory, n/w bandwidth), see how system performs
2. Increase load and see how much additional resources are needed

### Measure performance :
Use percentile
- Mediian / p50 : half way point
- 95th percentile / p95 :  95% of requests faster than thresold
- 99th percentile / p99 :  99% of requests faster than thresold
- 99.9th percentile / p999 :  99.9% of requests faster than thresold

## Fix 

### Scaling up/ Vertical scaling
Get heavy resources servers, there are limits
### Scaling out/  horizontal scaling
Add more servers also known as shared nothing architecture
It could be autscaling ( elastic scaling) or manual scaling