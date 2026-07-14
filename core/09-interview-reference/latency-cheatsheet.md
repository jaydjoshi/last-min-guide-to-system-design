# Latency Cheatsheet

##### Latency Numbers Every Engineer Should Know

| Operation | Time |
|------------|-------|
| L1 Cache Reference | 0.5 ns |
| Branch Mispredict | 5 ns |
| L2 Cache Reference | 7 ns |
| Mutex Lock/Unlock | 100 ns |
| Main Memory Reference | 100 ns |
| SSD Random Read | 150 μs |
| Disk Seek | 10 ms |
| Network Request (same datacenter) | 0.5 ms |
| Network Request (different datacenter) | 100 ms+ |

##### Key Takeaway

Memory is much faster than disk.

Memory/ L caches/ mutex seek : nano-seconds
SSD seek : micro-seconds
Disk seek : milli-seconds

```text
CPU Cache << RAM << SSD << HDD << Network
```