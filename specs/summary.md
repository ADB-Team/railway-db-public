# Summary

Document to analyze the best improvements to apply on each transaction.

## Runtimes differences

### All runtimes
- [Indexes](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_indexes.md)
- [Partitions](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_partitions.md)
- [Columnar storage Zedstore](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_zedstore.md)
- [Columnar storage Cstore](https://github.com/ADB-Team/railway-db-public/blob/main/doc/runtimes_zedstore.md)

### Diff overview

||Transaction 1|Transaction 3|Transaction 5   |Transaction 7|Backup transaction 2|Backup transaction 4|Backup transaction 5|
|----|-------------|-------------|----------------|-------------|--------------------|--------------------|--------------------|
|Hash index|0,081        |-0,126       |1,455           |-0,217       |1,119               |**-0,688**              |1,996               |
|B-tree index|**-0,045**       |-0,067       |**-1,317**          |0,2657       |0,0866              |0,493               |-2,007              |
|Partition 1|0,0428       |0,0397       |1,5985          |2,0383       |-0,0993            |-0,074              |**-3,9392**             |
|Partition 2|0,0656       |-0,0804      |2,4904          |2,3358       |0,0138              |0,0601              |-0,1546             |
|Partition 3|0,088        |0,0637       |1,8554          |6,3542       |0,0749              |-0,0282             |-0,1624             |
|Partition 4|0,0761       |-            |2,5292          |**-1,7839**      |-0,0011             |-0,1599             |-0,1146             |
|Partition 5|0,1031       |-            |2,4244          |2,0142       |0,1136              |0,0091              |-0,2793             |
|Zedstore CS-1|-0,0072      |**−1,191**      |3,2292          |3,0834       |0,3525              |0,0823              |1,0984              |
|Zedstore CS-3|**-0,0329**      |0,0521       |3,2489          |2,726        |0,3004              |-0,0014             |0,9658              |
|Zedstore J-1|0,0939       |0,118        |4,397           |3,3317       |0,4409              |-0,6689             |1,0871              |
|Zedstore J-2|0,0044       |0,0132       |3,136           |3,0057       |0,3361              |0,07                |-3,2908             |
|Cstore CS-1|0,0054       |-            |3,2392          |3,0944       |0,3509              |0,0806              |1,0509              |
|Cstore CS-2|0,1069       |0,8016       |3,1476          |2,6711       |0,4321              |1,0604              |0,3191              |
|Cstore J-1|-0,0108      |**-0,3577**      |1,7584          |-0,1047      |**-0,1159**            |**-2,9067**             |0,3706              |
|Cstore J-2|0,047        |-0,1165      |**-0,0141**         |**-0,3613**      |**-0,1362**             |-0,309              |**-6,2677**             |

## Best performance improvements 

### Transaction 1

[Query Plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)

- Best improvement: [B-tree indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)
- Second best improvement: [Columnar Store 3 with zedstore](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-3)

### Transaction 3

[Query Plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)

- Best improvement: [Zedstore Columnar Store 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-1)
- Second best improvement: [Cstore Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2)

### Transaction 5

[Query Plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)

- Best improvement: [Hash indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)
- Second best improvement: [Cstore Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2)

### Transaction 7

[Query Plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md)

- Best improvement: [Partition 4](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-4)
- Second best improvement: [Cstore Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2)

### Backup Transaction 2

[Query Plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)

- Best improvement: [Cstore Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2)
- Second best improvement: [Cstore Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1)

### Backup Transaction 4

[Query Plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)

- Best improvement: [Cstore Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2)
- Second best improvement: [Hash indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)

### Backup Transaction 5

[Query Plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)

- Best improvement: [Join Group 2 with cstore](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2)
- Second best improvement: [Partition 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-1)
