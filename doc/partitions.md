# Partition 1

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-1)

## Transaction 1

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition1/transaction1.md)

### Performance

- old avg: 0.5306 s
- new avg: 0.5734 s
- diff(avg): + 0.0428 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

This partitions is not used in the query plan, so the partitions not give us a better performance.

## Transaction 3

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition1/transaction3.md)

### Performance

- old avg: 3.2264 s
- new avg: 3.2661 s
- diff(avg): + 0.0397 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

A sequential searching is performed through the cities partitions. This not provides a better performance due to the big amount of partitions scanned.

## Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition1/transaction5.md)

### Performance

- old avg: 4.3591 s
- new avg: 5.9576 s
- diff(avg): + 1.5985 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Transaction 7

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition1/transaction7.md)

### Performance

- old avg: 1.9664 s
- new avg: 4.043 s
- diff(avg): + 2.0383 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

A sequential searching is performed through the cities partitions. This not provides a better performance due to the big amount of partitions scanned.


## Backup Transaction 2

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition1/backup-transaction2.md)

### Performance

- old avg: 1.0949 s
- new avg: 1.0856 s
- diff(avg): -0.0093 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

We can see how the partitions is used in the deleting cities part, as only a partition is used due to the division by country. This give as a smaller better performance for not loop over all cities.


## Backup Transaction 4

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition1/backup-transaction4.md)

### Performance

- old avg: 1.5775 s
- new avg: 1.5035 s
- diff(avg): -0.074 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The performance is so similar due to the transaction is not using the new partition.


## Backup Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition1/backup-transaction5.md)

### Performance

- old avg: 4.0755 s
- new avg: 0.1363 s
- diff(avg): -3.9392 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

In this case we can see a huge better performance in the query due to a parallel scan of cities in the main query. This allows the query to obtain the result so much faster than before.



# Partition 2

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-2)

## Transaction 1

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition2/transaction1.md)

### Performance

- old avg: 0.5306 s
- new avg: 0.5962 s
- diff(avg): + 0.0656 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

Seats partitions is used here but the performance is not better. This could be due to the sequential scans of all partitions instead of scanning the table in a row.

## Transaction 3

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition2/transaction3.md)

### Performance

- old avg: 3.2264 s
- new avg: 3.146 s
- diff(avg): -0.0804 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The performance is similar due to the fact that the partition is not used in this transaction.


## Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition2/transaction5.md)

### Performance

- old avg: 4.3591 s
- new avg: 6.8495 s
- diff(avg): + 2.4904 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

Seats partitions is used here but the performance is not better. This could be due to the sequential scans of all partitions instead of scanning the table in a row.




## Transaction 7

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition2/transaction7.md)

### Performance

- old avg: 1.9664 s
- new avg: 4.3022 s
- diff(avg): + 2.3358 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The partition is not used but the time is so much worst than before. This could be happening because of some subquery implementation with the partition.


## Backup Transaction 2

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition2/backup-transaction2.md)

### Performance

- old avg: 1.0949 s
- new avg: 1.1087 s
- diff(avg): + 0.0138 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The time is so similar because of not be using the new partition in any part of the transaction.


## Backup Transaction 4

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition2/backup-transaction4.md)

### Performance

- old avg: 1.5775 s
- new avg: 1.6376 s
- diff(avg): + 0.0601 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

Seats partitions is used here but the performance is not better. This should be better due to the parallel scans of all partitions instead of scanning the table in a row, but the time is worst.


## Backup Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition2/backup-transaction5.md)

### Performance

- old avg: 4.0755 s
- new avg: 3.9209 s
- diff(avg): -0.1546 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The time is better but the partition is not used anywhere in the query plan. This could be happening because of the use of the partition in some subquery.



# Partition 3

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-3)

## Transaction 1

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partitions3.md#transaction-1)

### Performance

- old avg: 0.5306 s
- new avg: 0.6186 s
- diff(avg): + 0.088 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation
It's not use a lot so we don't see different
## Transaction 3

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

### Performance

- old avg: 3.2264 s
- new avg: 3.2901 s
- diff(avg): + 0.0637 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation
Not used

## Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partitions3.md#transaction-5)

### Performance

- old avg: 4.3591 s
- new avg: 6.2145 s
- diff(avg): + 1.8554 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation
The cost is more important for each connection SQL found, so the time is longer

## Transaction 7

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partitions3.md#transaction-7)

### Performance

- old avg: 1.9664 s
- new avg: 8.3206 s
- diff(avg): + 6.3542 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation
Delete much time for each partition and for every he found.

## Backup Transaction 2

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

### Performance

- old avg: 1.0949 s
- new avg: 1.1698 s
- diff(avg): + 0.0749 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation
Not used 

## Backup Transaction 4

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

### Performance

- old avg: 1.5775 s
- new avg: 1.4947 s
- diff(avg): -0.0828 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation
Not used 

## Backup Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

### Performance

- old avg: 4.0755 s
- new avg: 3.9131 s
- diff(avg): -0.1624 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation
Not used 

# Partition 4

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-4)

## Transaction 1

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition4/transaction1.md)

### Performance

- old avg: 0.5306 s
- new avg: 0.6067 s
- diff(avg): + 0.0761 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation
Not used because of the number of rows
<!-- ## Transaction 3

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition2/backup-transaction5.md)

### Performance

- old avg: 3.2264 s
- new avg: 0.4126 s
- diff(avg): -2.8138 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation -->


## Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition4/transaction5.md)

### Performance

- old avg: 4.3591 s
- new avg: 6.8883 s
- diff(avg): 2.5292 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The time is worst due to the sequential scanning of the schedule partitions.


## Transaction 7

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition4/transaction7.md)

### Performance

- old avg: 1.9664 s
- new avg: 0.1825 s
- diff(avg): -1.7839 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The partitions is used and the time is better due to the parallel operations in deleting in each partition.


## Backup Transaction 2

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition4/backup-transaction2.md)

### Performance

- old avg: 1.0949 s
- new avg: 1.0938 s
- diff(avg): -0.0011 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The time is so similar because the partition is not used.


## Backup Transaction 4

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition4/backup-transaction4.md)

### Performance

- old avg: 1.5775 s
- new avg: 1.4176 s
- diff(avg): -0.1599 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The time is so similar because the partition is not used.


## Backup Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition4/backup-transaction5.md)

### Performance

- old avg: 4.0755 s
- new avg: 3.9609 s
- diff(avg): -0.1146 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The time is so similar because the partition is not used.


# Partition 5

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-5)

## Transaction 1

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition5/transaction1.md)

### Performance

- old avg: 0.5306 s
- new avg: 0.6337 s
- diff(avg): + 0.1031 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

The partition is used but the time is not better because of the sequential scanning of the new partitions.

<!-- ## Transaction 3

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition5/transaction3.md)

### Performance

- old avg: 3.2264 s
- new avg: 0.5571 s
- diff(avg): -2.6693 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation -->


## Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition5/transaction5.md)

### Performance

- old avg: 4.3591 s
- new avg: 6.7835 s
- diff(avg): + 2.4244 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Transaction 7

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition5/transaction7.md)

### Performance

- old avg: 1.9664 s
- new avg: 3.9806 s
- diff(avg): + 2.0142 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 2

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition5/backup-transaction2.md)

### Performance

- old avg: 1.0949 s
- new avg: 1.2085 s
- diff(avg): + 0.1136 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 4

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition5/backup-transaction4.md)

### Performance

- old avg: 1.5775 s
- new avg: 1.5866 s
- diff(avg): + 0.0091 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/partitions/partition5/backup-transaction5.md)

### Performance

- old avg: 4.0755 s
- new avg: 3.7962 s
- diff(avg): -0.2793 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


<!-- # Partition 6

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-6)

## Transaction 1

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan]()

### Performance

- old avg: 0.5306 s
- new avg: 0.6115 s
- diff(avg): + 0.0809 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

## Transaction 3

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

### Performance

- old avg: 3.2264 s
- new avg: 4.1649 s
- diff(avg): + 0.9385 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan]()

### Performance

- old avg: 4.3591 s
- new avg: 6.3846 s
- diff(avg): + 2.0255 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Transaction 7

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan]()

### Performance

- old avg: 1.9664 s
- new avg: 4.7118 s
- diff(avg): + 2.7454 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 2

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

### Performance

- old avg: 1.0949 s
- new avg: 1.1266 s
- diff(avg): + 0.0317 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 4

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

### Performance

- old avg: 1.5775 s
- new avg: 1.8516 s
- diff(avg): + 0.2741 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

### Performance

- old avg: 4.0755 s
- new avg: 3.8013 s
- diff(avg): -0.2742 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation



# Partition 7

→ see [here](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-7)

## Transaction 1

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan]()

### Performance

- old avg: 0.5306 s
- new avg: 0.5995 s
- diff(avg): + 0.0689 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

## Transaction 3

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

### Performance

- old avg: 3.2264 s
- new avg: 3.1418 s
- diff(avg): -0.0846 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan]()

### Performance

- old avg: 4.3591 s
- new avg: 5.9846 s
- diff(avg): + 1.6255 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Transaction 7

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan]()

### Performance

- old avg: 1.9664 s
- new avg: 3.9884 s
- diff(avg): + 2.022 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 2

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

### Performance

- old avg: 1.0949 s
- new avg: 1.0975 s
- diff(avg): + 0.0026 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 4

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

### Performance

- old avg: 1.5775 s
- new avg: 1.9942 s
- diff(avg): + 0.4167 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

### Performance

- old avg: 4.0755 s
- new avg: 3.783 s
- diff(avg): -0.2925 s

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation



# All partitions together

## Transaction 1

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [new query plan]()

### Performance

- old avg: 0.5306 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation

## Transaction 3

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [new query plan]()

### Performance

- old avg: 3.2264 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [new query plan]()

### Performance

- old avg: 4.3591 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Transaction 7

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)
- [new query plan]()

### Performance

- old avg: 1.9664 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 2

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [new query plan]()

### Performance

- old avg: 1.0949 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 4

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)
- [new query plan]()

### Performance

- old avg: 1.5775 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation


## Backup Transaction 5

### Query plans

- [old query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)
- [new query plan]()

### Performance

- old avg: 4.0755 s
- new avg:
- diff(avg):

→ all runtimes [here](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)

### Explanation -->
