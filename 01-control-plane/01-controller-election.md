
# Question 01: How Is the Kafka Controller Elected? (ZooKeeper vs KRaft)

## Answer

The **controller** is a special Kafka broker responsible for **cluster-wide coordination**. Only **one controller** is active at any time.

---

## ZooKeeper Mode (Legacy)

### Election Mechanism

* All brokers attempt to create the same **ephemeral znode**:

  ```
  /controller
  ```
* The broker that succeeds becomes the controller.
* Others remain standby.

### Key Steps

1. Broker registers under `/brokers/ids/<id>`
2. Race to create `/controller`
3. Winner increments `/controller_epoch`
4. Controller initializes metadata and triggers leader elections

### Characteristics

* Race-based election
* External ZooKeeper dependency
* Slower failover

---

## KRaft Mode (Modern Kafka)

### Election Mechanism

* Controller election is **Raft leader election**
* The Raft leader of the metadata quorum becomes the controller

### Key Steps

1. Nodes start as followers
2. Election timeout triggers candidacy
3. Vote requests sent to quorum
4. Majority vote elects leader

### Characteristics

* Consensus-based
* Strong consistency
* Faster and safer failover

---

## Interview One-Liner

> ZooKeeper elects controllers by race; KRaft elects controllers by consensus.
