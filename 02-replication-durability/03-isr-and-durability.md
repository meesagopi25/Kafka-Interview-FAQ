# Question 03: What Is ISR and How Does It Prevent Data Loss?

## Answer

ISR (**In-Sync Replicas**) is the set of replicas that are fully caught up with the leader.

---

## Why ISR Matters

* Prevents stale replicas from becoming leaders
* Guarantees durability
* Protects against data loss

---

## Critical Configurations

```properties
acks=all
min.insync.replicas=2
```

If ISR drops below the minimum, writes fail intentionally.

---

## Interview One-Liner

> ISR is Kafka’s durability contract — availability is sacrificed before data loss.

