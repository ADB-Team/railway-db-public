# Summary

## Transaction 1
### Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 0.5306 s | 0.411 s | 0.56 s | --   |
| Best: [B-tree indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)                | 10 | 0.535 s | 0.396 s | 0.64 s | + 0.0044 s |
| 2nd best: [Columnar Store 3](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-3) zedstore |    |          |         |        |      |

### Analysis

#### Best optimisation

The running time is actually microscopically bigger than without optimisation. The problem with optimising this query is that it already is quite fast, thus improvements don't show much of an impact.

If we take a look at the [query plan](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/with-indexes/transaction1_btree.md), we can see that one of the created Btree indexes actually is used. However, it is only used for a single scan that was already very fast to begin with.

#### 2nd best optimisation

## Transaction 3
### Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 3.2264 s | 3.047 s | 3.727 s | --   |
| Best: [Columnar Store 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-1) zedstore                |    |          |         |        |      |
| 2nd best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore |    |          |         |        |      |

### Analysis

## Transaction 5
### Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 4.3591 s | 4.14 s | 4.953 s | --   |
| Best: [Hash indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)                |    |          |         |        |      |
| 2nd best: [Columnar Store 3](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-3) zedstore |    |          |         |        |      |

### Analysis

## Transaction 7
### Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 1.9664 s | 1.948 s | 2.012 	 s | --   |
| Best: [Partition 4](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-4)                |    |          |         |        |      |
| 2nd best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore |    |          |         |        |      |

### Analysis

## Backup Transaction 2
### Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 1.0949 s | 1.06 s | 1.155 s | --   |
| Best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore                |    |          |         |        |      |
| 2nd best: [Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1) cstore |    |          |         |        |      |

### Analysis

## Backup Transaction 4
### Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 1.5775 s | 1.501 s | 1.867 s | --   |
| Best: [Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1) cstore                |    |          |         |        |      |
| 2nd best: [Hash indexes](https://github.com/ADB-Team/railway-db-public/blob/main/specs/indexes.md)  |    |          |         |        |      |

### Analysis

## Backup Transaction 5
### Runtimes

| Database version                   | n  | avg      | min     | max    | diff |
|-------------------------------------|----|----------|---------|--------|------|
| Without optimisation                | 10 | 4.0755 s | 3.786 s | 4.238 	 s | --   |
| Best: [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2) cstore                |    |          |         |        |      |
| 2nd best: [Partition 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/partitions.md#partition-1) |    |          |         |        |      |

### Analysis
