
# Question 06: What Are the Major Kafka Scaling Bottlenecks and How Do You Solve Them?

## Answer

Kafka scaling depends on **partition parallelism** and resource balance.

---

## Common Bottlenecks

| Bottleneck | Cause               | Solution                     |
| ---------- | ------------------- | ---------------------------- |
| Controller | Too many partitions | KRaft, dedicated controllers |
| Disk       | High retention      | Add brokers, tiered storage  |
| Network    | Replication traffic | Compression, rack awareness  |
| Consumers  | Low parallelism     | Increase partitions          |

---

## Key Rule

> Brokers scale only when partitions scale.

---

## Interview One-Liner

> Kafka scales horizontally only if you design for partition parallelism.
