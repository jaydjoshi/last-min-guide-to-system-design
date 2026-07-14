# LSM vs B-Tree vs Hash index


| Feature                   | Hash Index              | LSM Tree                    | B-Tree              |
| ------------------------- | ----------------------- | --------------------------- | ------------------- |
| Point Lookups             | Excellent               | Good                        | Excellent           |
| Writes                    | Very fast               | Very fast                   | Moderate            |
| Reads                     | Excellent (exact match) | Good                        | Excellent           |
| Range Queries             | No                      | Good                        | Excellent           |
| Sequential Write Friendly | No                      | Yes                         | No                  |
| Compaction                | No                      | Required                    | No                  |
| Memory Dependence         | High                    | Moderate                    | Lower               |
| Typical Use               | Cache / KV              | Write-heavy distributed DBs | OLTP relational DBs |