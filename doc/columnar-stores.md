# Zedstore

## General conclusions

The setup process of zedstore is not an easy one as it isn't an extension but a fork of Postgres. Included into the main version, however, it would be the easiest to use of all options.

Join groups were made with materialized views which proved to be quite successful.

**Issues:**

- no support for join groups, also not as an workaround with views [as suggested in our specification file](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#postgres-specific-implementation)
- creating a columnar store with a huge amount of rows (900 472 in case of columnar store 2) seems not to be possible, maybe too much overhead on creation?
- when copying data with auto-increment ID from a row store into a columnar store, the auto-incremation starts at 0 again for new entries, despite there being more than 0 entries already in the columnar store
- after some transactions, costly catalog updates are made, see [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/catalog-update.txt) for an extract

## Columnar Store 1

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-1)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/transaction1.md)

#### Performance

- old avg: 0.5306 s
- new avg: 0.5233 s
- diff(avg): -0.0072 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

As expected, the query runs slightly faster. The difference is almost unnoticable though because `routes` is only accessed in a single small part of the query, the selecting of the connection.

### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/transaction3.md)

#### Performance

- old avg: 3.2264 s
- new avg: 0.0552 s
- diff(avg): -3.1712 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

When bulk-inserting the old data from the `routes` row store table, their ID's are copied as well. For some reason, when inserting new rows into the newly created `routes` columnar store, not the next ID in row is taken but it's counting from 0 up again. Therefore, this transaction fails. We can mitigate this issue by inserting the data without ID's and letting the ID's be automatically incremented, however this leads to inconsistencies with our data, as the ID's are then newly distributed, thus making other transactions fail instead.

When temporarily changing the creation process of columnar store 3 to create fresh ID's for the data from `routes` row store, we can also test this method and get **2.488 s** as a runtime which is also quicker than before, just as expected.

### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/transaction5.md)

#### Performance

- old avg: 4.3591 s
- new avg: 7.5883 s
- diff(avg): + 3.2292 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

We expected a performance improvement, however the query runs much slower. There is no apparent reason to see for that in the query plan. It might be the catalog update that follows the transaction, which for readability reasons was not included into the formatted query plan. [Here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/catalog-update.txt) you can find an excerpt.

### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/transaction7.md)

#### Performance

- old avg: 1.9664 s
- new avg: 5.0498 s
- diff(avg): + 3.0834 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

`routes` isn't even accessed in this query, so the perfomance loss cannot be traced to the usage of the columnar store. Instead, it could be because of a catalog update which follows the execution of this transaction.

### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/backup-transaction2.md)

#### Performance

- old avg: 1.0949 s
- new avg: 1.4474 s
- diff(avg): + 0.3525 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

There is almost no perfomance difference which can be explained by the fact, that `routes` is not accessed in this transaction so the implementation of `routes` as a columnar store has no impact.

### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/backup-transaction4.md)

#### Performance

- old avg: 1.5775 s
- new avg: 1.6598 s
- diff(avg): + 0.0823 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

There is almost no perfomance difference which can be explained by the fact, that `routes` is not accessed in this transaction so the implementation of `routes` as a columnar store has no impact.

### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs1/backup-transaction5.md)

#### Performance

- old avg: 4.0755 s
- new avg: 5.1739 s
- diff(avg): + 1.0984 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

We expected a speed-up but instead got a slow-down. This might be due to the join of `routes` with `stations` or due to [catalog updates](https://github.com/ADB-Team/railway-db-public/blob/main/doc/catalog-update.txt).

## Columnar Store 2

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#columnar-store-2)

This columnar store couldn't be created with zedstore. During the multiple creation attempts, the 16 GB of RAM overflowed each time.


## Columnar Store 3

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#columnar-store-3)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs3/transaction1.md)

#### Performance

- old avg: 0.5306 s
- new avg: 0.4978 s
- diff(avg): -0.0329 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

The new `station_names_cs` is used which improves the query ever so slightly. It isn't the heaviest part of the transaction though, which is why the impact is quite low.

### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs3/transaction3.md)

#### Performance

- old avg: 3.2264 s
- new avg: 3.2785 s
- diff(avg): + 0.0521 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

The perfomance is slightly worse, probably due to the overhead of inserting into the columnar store in this transaction.

### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs3/transaction5.md)

#### Performance

- old avg: 4.3591 s
- new avg: 7.608 s
- diff(avg): + 3.2489 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

The new `station_names_cs` is only used once for a sequential scan which shouldn't slow down the transaction this much. The actual reason might be a [catalog update](https://github.com/ADB-Team/railway-db-public/blob/main/doc/catalog-update.txt) which follows the execution of this transaction.

### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs3/transaction7.md)

#### Performance

- old avg: 1.9664 s
- new avg: 4.6924 s
- diff(avg): + 2.726 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

The newly created columnar store is not used in this transaction. The significant performance loss might be because of [catalog operations](https://github.com/ADB-Team/railway-db-public/blob/main/doc/catalog-update.txt).

### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs3/backup-transaction2.md)

#### Performance

- old avg: 1.0949 s
- new avg: 1.3953 s
- diff(avg): + 0.3004 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

`station_names_cs` is not used in this transaction as there is no part that requires station names.

### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs3/backup-transaction4.md)

#### Performance

- old avg: 1.5775 s
- new avg: 1.5761 s
- diff(avg): -0.0014 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

The newly created columnar store is not used in this transaction as there is no use of station names, any perfomance differences are probably due to fluctuations.

### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/cs3/backup-transaction5.md)

#### Performance

- old avg: 4.0755 s
- new avg: 5.0413 s
- diff(avg): + 0.9658 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

As this transaction needs to access other columns of `stations` as well, it cannot make use of the new columnar store.

## Join Group 1

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#join-group-1)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg1/transaction1.md)

#### Performance

- old avg: 0.5306 s
- new avg: 0.6245 s
- diff(avg): + 0.0939 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

The new columnar store is used but only for a single sequential scan so it doesn't have much of an impact on the performance.

### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg1/transaction3.md)

#### Performance

- old avg: 3.2264 s
- new avg: 3.3443 s
- diff(avg): + 0.118 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `seats`, `wagons` and `trains`, thus making no use of the newly created join group.

### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg1/transaction5.md)

#### Performance

- old avg: 4.3591 s
- new avg: 8.7561 s
- diff(avg): + 4.397 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction does use the new join group. The high performance losses are probably caused by the [catalog operations](https://github.com/ADB-Team/railway-db-public/blob/main/doc/catalog-update.txt).

### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg1/transaction7.md)

#### Performance

- old avg: 1.9664 s
- new avg: 5.298125 s
- diff(avg): 3.331725 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `seats`, `wagons` and `trains`, thus making no use of the newly created join group.

### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg1/backup-transaction2.md)

#### Performance

- old avg: 1.0949 s
- new avg: 1.535875 s
- diff(avg): + 0.440975 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `seats`, `wagons` and `trains`, thus making no use of the newly created join group.

### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg1/backup-transaction4.md)

#### Performance

- old avg: 1.5775 s
- new avg: 0.908625 s
- diff(avg): -0.668875 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction makes use of the new join group and is indeed sped up a little.

### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg1/backup-transaction5.md)

#### Performance

- old avg: 4.0755 s
- new avg: 5.162625 s
- diff(avg): + 1.087125 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `seats`, `wagons` and `trains`, thus making no use of the newly created join group.

## Join Group 2

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#join-group-2)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg2/transaction1.md)

#### Performance

- old avg: 0.5306 s
- new avg: 0.535 s
- diff(avg): + 0.0044 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `contries` and `cities` thus making no use of the newly created join group.

### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg2/transaction3.md)

#### Performance

- old avg: 3.2264 s
- new avg: 3.239625 s
- diff(avg): + 0.013225 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `contries` and `cities` thus making no use of the newly created join group.

### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg2/transaction5.md)

#### Performance

- old avg: 4.3591 s
- new avg: 7.495125 s
- diff(avg): + 3.136025 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `contries` and `cities` thus making no use of the newly created join group. The high performance losses are likely caused by the [catalog operations](https://github.com/ADB-Team/railway-db-public/blob/main/doc/catalog-update.txt).

### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg2/transaction7.md)

#### Performance

- old avg: 1.9664 s
- new avg: 4.972125 s
- diff(avg): + 3.005725 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `contries` and `cities` thus making no use of the newly created join group. The high performance losses are likely caused by the [catalog operations](https://github.com/ADB-Team/railway-db-public/blob/main/doc/catalog-update.txt).

### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg2/backup-transaction2.md)

#### Performance

- old avg: 1.0949 s
- new avg: 1.431 s
- diff(avg): + 0.3361 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `contries` and `cities` thus making no use of the newly created join group.

### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg2/backup-transaction4.md)

#### Performance

- old avg: 1.5775 s
- new avg: 1.6475 s
- diff(avg): + 0.07 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction doesn't include a join of `contries` and `cities` thus making no use of the newly created join group.

### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/columnar-stores/zedstore/jg2/backup-transaction5.md)

#### Performance

- old avg: 4.0755 s
- new avg: 0.784625 s
- diff(avg): -3.290875 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

This transaction makes use of the new join group and has significant performance improvements due to that.


## Cstore

## General conclusions

## Columnar Store 1

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-1)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan]()

#### Performance

- old avg: 0.5306 s
- new avg: 
- diff(avg): 

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

#### Performance

- old avg: 3.2264 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan]()

#### Performance

- old avg: 4.3591 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan]()

#### Performance

- old avg: 1.9664 s
- new avg:
- diff(avg): 

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

#### Performance

- old avg: 1.0949 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

#### Performance

- old avg: 1.5775 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

#### Performance

- old avg: 4.0755 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation



## Columnar Store 2

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#columnar-store-2)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan]()

#### Performance

- old avg: 0.5306 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

#### Performance

- old avg: 3.2264 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan]()

#### Performance

- old avg: 4.3591 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan]()

#### Performance

- old avg: 1.9664 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

#### Performance

- old avg: 1.0949 s
- new avg:
- diff(avg): 

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

#### Performance

- old avg: 1.5775 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

#### Performance

- old avg: 4.0755 s
- new avg:
- diff(avg): 

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation



## Columnar Store 3

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#columnar-store-3)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan]()

#### Performance

- old avg: 0.5306 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation

### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

#### Performance

- old avg: 3.2264 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan]()

#### Performance

- old avg: 4.3591 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan]()

#### Performance

- old avg: 1.9664 s
- new avg:
- diff(avg): 

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

#### Performance

- old avg: 1.0949 s
- new avg:
- diff(avg): 

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

#### Performance

- old avg: 1.5775 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

#### Performance

- old avg: 4.0755 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cs.md)

#### Explanation


## Join Group 1

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#join-group-1)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan]()

#### Performance

- old avg: 1.0671 s
- new avg: 1.0562 s
- diff(avg): -0.0108 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation

### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

#### Performance

- old avg: 3.1163 s
- new avg: 2.7586 s
- diff(avg): -0.3577 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used

### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan]()

#### Performance

- old avg: 11.8078 s
- new avg: 13.5663 s
- diff(avg): 1.7584 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation


### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan]()

#### Performance

- old avg: 7.9865 s
- new avg: 7.8817 s
- diff(avg): -0.1047 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used

### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

#### Performance

- old avg: 2.4197 s
- new avg: 2.3037 s
- diff(avg): -0.1159 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used

### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

#### Performance

- old avg: 4.3216 s
- new avg: 1.4148 s
- diff(avg): -2.9067 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation


### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

#### Performance

- old avg: 6.5941 s
- new avg: 6.9701 s
- diff(avg): 0.3760 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used

## Join Group 2

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#join-group-2)

### Transaction 1

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan]()

#### Performance

- old avg: 1.0671 s
- new avg: 1.1141 s
- diff(avg): 0.0470 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used
### Transaction 3

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

#### Performance

- old avg: 3.1163 s
- new avg: 2.9998 s
- diff(avg): -0.1165 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used

### Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan]()

#### Performance

- old avg: 11.8078 s
- new avg: 11.7937 s
- diff(avg): -0.0141 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used

### Transaction 7

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan]()

#### Performance

- old avg: 7.9865 s
- new avg: 7.6251 s
- diff(avg): -0.3613 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation


### Backup Transaction 2

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

#### Performance

- old avg: 2.4197 s
- new avg: 2.2835
- diff(avg): -0.1362 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used

### Backup Transaction 4

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

#### Performance

- old avg: 4.3216 s
- new avg: 4.0125 s
- diff(avg): -0.3090 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation
not used 

### Backup Transaction 5

#### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

#### Performance

- old avg: 6.5941 s
- new avg: 0.3263 s
- diff(avg): -6.2677

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_cstore.md)

#### Explanation

