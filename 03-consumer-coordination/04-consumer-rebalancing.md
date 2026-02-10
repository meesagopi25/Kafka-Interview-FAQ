# Question 04: How Does Kafka Handle Consumer Group Rebalancing Internally?

## Answer

Rebalancing redistributes partitions among consumers to maintain **correctness and parallelism**.

---

## Triggers

* Consumer joins or leaves
* Consumer crash
* Partition count change

---

## Internal Flow

1. Group coordinator detects change
2. Consumption is paused
3. New partition assignment computed
4. Assignment distributed
5. Consumption resumes

---

## Mitigation Strategies

```properties
partition.assignment.strategy=cooperative-sticky
group.instance.id=<static-id>
```

---

## Interview One-Liner

> Rebalancing trades short-term unavailability for long-term correctness.

