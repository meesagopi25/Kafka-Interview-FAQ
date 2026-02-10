# Kafka-Interview-FAQ

# Kafka Architecture – Interview & Reference Guide

This repository contains **Kafka architect–level interview questions**, grouped by **logical subsystems** of Kafka. Each question is maintained as an individual markdown file for easy reference and reuse.

## 📂 Folder Structure

```
kafka-architecture-interview/
├── 01-control-plane/
├── 02-replication-durability/
├── 03-consumer-coordination/
├── 04-processing-guarantees/
├── 05-scaling-performance/
└── README.md
```

## 📘 Question Index

### 01. Control Plane

* [Question 01 – How is the Kafka controller elected?](01-control-plane/01-controller-election.md)

### 02. Replication & Durability

* [Question 02 – How does partition leader election work when a broker fails?](02-replication-durability/question-02-partition-leader-election.md)
* [Question 03 – What is ISR and how does it prevent data loss?](02-replication-durability/question-03-isr-and-durability.md)

### 03. Consumer Coordination

* [Question 04 – How does Kafka handle consumer group rebalancing internally?](03-consumer-coordination/question-04-consumer-rebalancing.md)

### 04. Processing Guarantees

* [Question 05 – How does Kafka achieve exactly-once semantics (EOS)?](04-processing-guarantees/question-05-exactly-once-semantics.md)

### 05. Scaling & Performance

* [Question 06 – What are the major Kafka scaling bottlenecks and how do you solve them?](05-scaling-performance/question-06-scaling-bottlenecks.md)

---

## 🎯 How to Use This Repo

* Interview preparation for **Kafka Architect / Staff Engineer roles**
* Internal Kafka platform documentation
* On-call and incident response reference
* Knowledge sharing and mentoring

---

> Kafka is best understood by separating **control plane, durability, coordination, guarantees, and scale**.
