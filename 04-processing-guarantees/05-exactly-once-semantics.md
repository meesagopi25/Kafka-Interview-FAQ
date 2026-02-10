# Question 05: How Does Kafka Achieve Exactly-Once Semantics (EOS)?

## Answer

Exactly-once semantics ensures each record affects downstream state **exactly once**, even during failures.

---

## Core Building Blocks

* Idempotent producers
* Transactions
* Read-process-write atomicity

---

## Transaction Flow

```
BeginTxn → Consume → Process → Produce → CommitTxn
```

---

## Required Configurations

```properties
enable.idempotence=true
transactional.id=txn-1
isolation.level=read_committed
```

---

## Interview One-Liner

> Kafka’s EOS is transactional consistency with a clearly defined scope.

