# Question 02: How Does Partition Leader Election Work When a Broker Fails?

## Answer

Partition leader election ensures **availability without data loss** and is handled **only by the controller**.

---

## Step-by-Step Flow

1. Controller detects broker failure
2. Affected partitions are identified
3. Failed broker removed from ISR
4. New leader selected **from ISR only**
5. Metadata updated and published

---

## Guarantees

* No data loss (with ISR rules)
* Ordering preserved per partition

---

## Interview One-Liner

> Kafka never elects a leader outside ISR unless explicitly configured to risk data loss.

