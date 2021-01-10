# Zedstore

## General conclusions

The setup process of zedstore is not an easy one as it isn't an extension but a fork of Postgres. Included into the main version, however, it would be the easiest to use of all options.

**Issues:**

- no support for join groups, also not as an workaround with views [as suggested in our specification file](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#postgres-specific-implementation)
- creating a columnar store with a huge amount of rows (900 472 in case of columnar store 2) seems not to be possible, maybe too much overhead on creation?

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


## Join Group 2

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#join-group-2)

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

