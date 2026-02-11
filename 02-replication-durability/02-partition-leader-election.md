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

Perfect — this is a **core Kafka architect interview question**, and now we’ll go **deep**, including:

* Internal control-plane flow
* ISR mechanics
* Metadata updates
* ZK vs KRaft differences
* Real commands to observe it live
* Failure edge cases

---

# Question 02: How Does Partition Leader Election Work When a Broker Fails?

---

# 1️⃣ First, Understand the Rule

When a broker fails:

> Only the **controller** performs partition leader election.

It does **NOT** happen peer-to-peer.

---

# 2️⃣ What Triggers Partition Leader Election?

Partition leader election happens when:

* Broker crashes
* Broker is killed (`kill -9`)
* Network partition
* Broker shutdown without clean leader transfer

---

# 3️⃣ High-Level Flow

```
Broker fails
↓
Controller detects failure
↓
Remove broker from ISR
↓
For affected partitions:
   Elect new leader from ISR
↓
Update metadata
↓
Notify brokers
↓
Clients refresh metadata
```

---

# 4️⃣ Detailed Step-by-Step (KRaft Mode – Modern Kafka)

We’ll explain KRaft first (ZooKeeper is covered later).

---

## 🔹 Step 1: Broker Failure Detection

The controller tracks broker heartbeats.

When a broker stops responding:

```
Broker heartbeat timeout
```

Internally:

```
Broker marked as FENCED / OFFLINE
```

---

### 🔍 Observe Broker State

```bash
kafka-broker-api-versions.sh \
  --bootstrap-server localhost:9092
```

If broker is down, it won’t appear.

---

## 🔹 Step 2: Controller Identifies Affected Partitions

Controller checks:

```
Which partitions had this broker as leader?
```

Example:

```
Topic: orders
Partition 0 → Leader: Broker 2
Partition 1 → Leader: Broker 3
```

If Broker 2 fails:

Partition 0 must elect new leader.

---

### 🔍 Check Partition State

```bash
kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --describe --topic orders
```

Example output:

```
Topic: orders  Partition: 0  Leader: 2  Replicas: 2,3,1  ISR: 2,3,1
```

---

## 🔹 Step 3: Remove Failed Broker from ISR

Before election:

```
ISR: 2,3,1
```

After broker 2 fails:

```
ISR: 3,1
```

This ensures:

> Only in-sync replicas can become leader.

---

## 🔹 Step 4: Leader Election Logic

For each affected partition:

```
New leader = first replica in ISR list
```

If ISR:

```
[3,1]
```

New leader becomes:

```
3
```

This is deterministic — not random.

---

## 🔹 Step 5: Metadata Log Update

In KRaft:

Controller appends a metadata record:

```
PartitionChangeRecord
```

This includes:

* New leader
* Updated ISR
* Leader epoch increment

---

### 🔍 Verify Controller

```bash
kafka-metadata-quorum.sh \
  --bootstrap-server localhost:9092 \
  describe --status
```

---

## 🔹 Step 6: Brokers Apply Metadata Update

Other brokers:

* Receive updated metadata
* Update in-memory partition state
* Followers begin replicating from new leader

---

## 🔹 Step 7: Clients Refresh Metadata

Producers/Consumers:

* Get `NOT_LEADER_OR_FOLLOWER`
* Refresh metadata automatically
* Resume traffic to new leader

---

# 5️⃣ What If ISR Is Empty?

Example:

```
Replication factor = 3
ISR = [2]
Broker 2 fails
```

Now:

```
ISR = []
```

Result:

* No eligible leader
* Partition becomes OFFLINE
* Producers get `NOT_ENOUGH_REPLICAS`

This prevents data loss.

---

# 6️⃣ ZooKeeper Mode – What Changes?

The election logic is the same.

Only metadata storage differs.

---

## 🔹 Step 1: Controller Detects Broker Failure

ZooKeeper detects session expiration.

You can observe:

```bash
zookeeper-shell.sh localhost:2181
ls /brokers/ids
```

Failed broker disappears.

---

## 🔹 Step 2: Controller Updates Partition State

Controller writes new leader to:

```
/brokers/topics/<topic>/partitions/<p>/state
```

Check:

```bash
get /brokers/topics/orders/partitions/0/state
```

Example:

```json
{"leader":3,"leader_epoch":4,"isr":[3,1]}
```

---

# 7️⃣ Commands to Simulate Broker Failure

### Stop broker gracefully

```bash
kafka-server-stop.sh
```

### Or simulate crash

```bash
kill -9 <pid>
```

---

# 8️⃣ Observe Election Live

### Watch partition leader change

```bash
watch -n 1 kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --describe --topic orders
```

---

### Check controller logs

```bash
grep -i "leader" server.log
```

Example:

```
Broker 2 is dead
Electing new leader 3 for partition orders-0
```

---

# 9️⃣ Leader Epoch (Important Concept)

Every time leadership changes:

```
leader_epoch++
```

Prevents:

* Stale leader serving traffic
* Split-brain

---

# 🔟 Edge Case: Unclean Leader Election

Config:

```properties
unclean.leader.election.enable=true
```

If enabled:

* Non-ISR replica may become leader
* Data loss possible

⚠️ Never enable in production.

---

# 1️⃣1️⃣ Interview-Level Summary

### Guarantees

* Leader always chosen from ISR
* No data loss (if properly configured)
* Ordering preserved per partition
* Election handled by controller only

---

# 🎯 Interview-Ready Answer

> When a broker fails, the controller removes it from ISR, deterministically selects a new leader from remaining ISR replicas, updates metadata (ZooKeeper or metadata log), increments leader epoch, and notifies brokers and clients. If ISR is empty, the partition remains offline to prevent data loss.

---

# 🔥 Strong One-Liner

> Partition leader election is deterministic, ISR-bound, and controller-driven.

---

# 🧠 Mental Model

```
Broker dies
↓
Controller detects
↓
Remove from ISR
↓
First ISR replica becomes leader
↓
Metadata update
↓
Clients refresh
```

---

If you want next, I can:

* Add this into your `question-02` GitHub file
* Explain leader election during **controlled shutdown**
* Show differences between **preferred leader election vs failure election**
* Explain how **high partition count impacts election time**

Just tell me 👍
