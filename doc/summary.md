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
| Without optimisation                | 10 | 3.002 s |2.874 s|3.295 s|--|
| 2nd best: [Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1) cstore |  10  |    0,9105 s      |   0,849 s     |    1,12 s   |  -2,0915 s    |

## Analysis

### Best optimisation

As one can see in the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/transaction3.md), transaction 3 heavily makes use of columnar store number 3, which is `routes`.

For each of the connections that are created, a sequential scan is done on `routes`. Accessing data is where the new columnar store is more performant than a regular row store. 

What is interesting to see is that this transaction also inserts into `routes` but not into the new columnar store but into the old row store that was renamed to `routes_rs`. This could also be a contributing factor to the performance as it is generally less efficient to insert into columnar stores than into row stores.

### 2nd best optimisation

We can see the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/cstore/jg1/transaction3.md) with the new costs and times within the explain analyze query plan.

The new table `seats_wagons_trains_jg` is not used directly in the transaction but the implementation provides a better average time.

# Transaction 5
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 11.81|11.17|12.69|--|
| Best: [Hash indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)                | 10   |   10.15|8.79|11.21|-1.66|
| 2nd best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore |  10  | 11.71 | 11.61|11.94|-0.10        |

## Analysis

### Best optimisation

### 2nd best optimisation

# Transaction 7
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 1.9664 s | 1.948 s | 2.012 	 s | --   |
| Best: [Partition 4](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-4)                | 10 | 0.1755 s | 0.172 s | 0.185 s | -1.7909 s |
| Without optimisation                | 10 | 4,4828 s | 4,153 s | 5,046 	 s | --   |
| 2nd best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore | 10 | 4,2814 s |4,137 s|4,512 s| -0,2014 s |         

## Analysis

### Best optimisation

Transaction 7 deletes connections from `schedule` and `passengers_schedule`. By partitioning the `schedule` table, those bulk deletions become much faster. As one can see in the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition4/transaction7.md), the deletions are made by accessing each partition of `schedule` separately with a sequential scan.

### 2nd best optimisation

Transaction 7 use a join of `countries` and `cities`. Joining that tables in a single table become faster. As one can see in the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/sctore/jg2/transaction7.md), the table `cities_countries_jg` is used in scans.

# Backup Transaction 2
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 2.42|2.23|2.82|--|
| Best: [Join Group 1 + Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1) cstore | 10   |  2.17|2.13|2.22|-0.25|

## Analysis

### Best opimisation

### 2nd best optimisation

# Backup Transaction 4
## Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 4.32|3.89|4.75|--|
| Best: [Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1) cstore                | 10 | 3.84|3.77|3.94|-0.48         | 
| Without optimisation                | 10 | 1.5775 s | 1.501 s | 1.867 s | --   |
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
| Without optimisation                | 10 | 6.59|6.40|6.87|--|
| Best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore                | 10   |9.52|9.01|11.22|2.92          |   
| Without optimisation                | 10 | 4.0755 s | 3.786 s | 4.238 	 s | --   |
| 2nd best: [Partition 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-1) | 10 | 0.149375 s | 0.136 s | 0.156 s | - 3.926125 s |

## Analysis

### Best optimisation

### 2nd best optimisation

This transaction makes great use of partition 1, which partitions cities by country. As you can see in the [query plan], it scans all the partitions in parallel, saving a lot of time.

# Final conclusions

Which of the above improvements would be suitable to permanently apply to our database system?

The optimisation that affects almost all of the transactions in a positive way is [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) implemented with cstore. A look at the [runtimes](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md) shows that except for transaction 1, which has a miniscule performance decrease of only 0.047 seconds, all transactions have performance improvements, some are even the highest improvements and therefore mentioned in the analysis above.

[Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1) implemented with cstore is also a hot candidate for applying permanently, however it would mean a significant performance loss for transaction 5 (see [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md) for the runtimes).

Another performance improvement that could be applied without harming other transactions that cannot make use of it, would be [B-tree indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)). As previously discussed, backup transaction 4 would neither profit nor lose performance and only two transactions are affected in a slightly negative way performance-wise, as the [running times table for the indexes](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_indexes.md) shows.

When it comes to the other options to improve performance that were tried out, [columnar stores](https://github.com/ADB-Team/railway-db-public/blob/main/doc/columnar-stores.md) and [partitions](https://github.com/ADB-Team/railway-db-public/blob/main/doc/partitions.md), there are always winners and losers for each of the modifications. This means it could potentially harm overall performance more than it would improve the transactions. 
