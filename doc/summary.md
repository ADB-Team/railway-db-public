# Summary

Documentation of the implementation of [the summary specification](https://github.com/ADB-Team/railway-db-public/blob/main/specs/summary.md).

Table of Contents
=================

   * [Transaction 1](#transaction-1)
   * [Transaction 3](#transaction-3)
   * [Transaction 5](#transaction-5)
   * [Transaction 7](#transaction-7)
   * [Backup Transaction 2](#backup-transaction-2)
   * [Backup Transaction 4](#backup-transaction-4)
   * [Backup Transaction 5](#backup-transaction-5)
   * [Final conclusions](#final-conclusions)

# Transaction 1
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 0.5306 s | 0.411 s | 0.56 s | --   |
| Best: [B-tree indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)                | 10 | 0.535 s | 0.396 s | 0.64 s | + 0.0044 s |
| 2nd best: [Columnar Store 3](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-3) zedstore | 10 | 0.4045 s | 0.395 s | 0.429 s | -0.1261 s |

## Analysis

### Best optimisation

The running time is actually microscopically bigger than without optimisation. The problem with optimising this query is that it already is quite fast, thus improvements don't show much of an impact.

If we take a look at the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/with-indexes/transaction1_btree.md), we can see that 2 of the created Btree indexes actually are used. However, it is only used for a single scan that was already very fast to begin with.

### 2nd best optimisation

Despite our historical data suggesting that the Columnar Store 3 with zedstore is the 2nd best optimisation, the newest runtime tests suggest that it actually is the best optimisation regarding transaction 1.

A look into the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs3/transaction1.md) shows us that the same part of the query as with the 1st optimisation is being improved, the getting of the connection. Accessing the station names from a columnar store apparently has a bigger performance impact than a btree index on `stations.name` and `schedule.start_time`.

# Transaction 3
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 3.2264 s | 3.047 s | 3.727 s | --   |
| Best: [Columnar Store 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-1) zedstore                | 10 | 2.47 s | 2.381 s | 2.569 s | - 0.75615 s |
| 2nd best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore |    |          |         |        |      |

## Analysis

### Best optimisation

As one can see in the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/transaction3.md), transaction 3 heavily makes use of columnar store number 3, which is `routes`.

For each of the connections that are created, a sequential scan is done on `routes`. Accessing data is where the new columnar store is more performant than a regular row store. 

What is interesting to see is that this transaction also inserts into `routes` but not into the new columnar store but into the old row store that was renamed to `routes_rs`. This could also be a contributing factor to the performance as it is generally less efficient to insert into columnar stores than into row stores.

### 2nd best optimisation

# Transaction 5
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 4.3591 s | 4.14 s | 4.953 s | --   |
| Best: [Hash indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)                |    |          |         |        |      |
| 2nd best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore |    |          |         |        |      |

## Analysis

### Best optimisation

### 2nd best optimisation

# Transaction 7
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 1.9664 s | 1.948 s | 2.012 	 s | --   |
| Best: [Partition 4](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-4)                | 10 | 0.1755 s | 0.172 s | 0.185 s | -1.7909 s |
| 2nd best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore |    |          |         |        |      |

## Analysis

### Best optimisation

Transaction 7 deletes connections from `schedule` and `passengers_schedule`. By partitioning the `schedule` table, those bulk deletions become much faster. As one can see in the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition4/transaction7.md), the deletions are made by accessing each partition of `schedule` separately with a sequential scan.

### 2nd best optimisation

# Backup Transaction 2
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 1.0949 s | 1.06 s | 1.155 s | --   |
| Best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore                |    |          |         |        |      |
| 2nd best: [Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1) cstore |    |          |         |        |      |

## Analysis

### Best opimisation

### 2nd best optimisation

# Backup Transaction 4
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 1.5775 s | 1.501 s | 1.867 s | --   |
| Best: [Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1) cstore                |    |          |         |        |      |
| 2nd best: [Hash indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)  | 10 | 1.2375 s | 1.065 s | 1.17 s | - 0.4537 s |

## Analysis

### Best optimisation

### 2nd best optimisation

Backup transaction 4 actually makes no use of the created indexes, see [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/with-indexes/backup-transaction4_hash.md). So why is there still the 2nd best performance improvement with hash indexes?

It might just be that that there was just a performance improvement when run on Ismael Pérez's computer. A re-run without indexes resulted in similar running times as the ones with hash indexes for backup transaction 4.

The nice performance improvement might just be a hardware improvement after all.

# Backup Transaction 5
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 4.0755 s | 3.786 s | 4.238 	 s | --   |
| Best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore                |    |          |         |        |      |
| 2nd best: [Partition 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-1) | 10 | 0.149375 s | 0.136 s | 0.156 s | - 3.926125 s |

## Analysis

### Best optimisation

### 2nd best optimisation

This transaction makes great use of partition 1, which partitions cities by country. As you can see in the [query plan], it scans all the partitions in parallel, saving a lot of time.

# Final conclusions

Which of the above improvements would be suitable to permanently apply to our database system?
