## MAIN QUERY
```sql
Query Text: /*
* Show number of seats per wagon in each train.
* → Count nr of seats, group by wagon and train.
*
* EXEC SCRIPT:
psql [DB-NAME] < transactions/backup_transaction4.sql
*/
GroupAggregate  (cost=134154.28..152163.72 rows=900472 width=44) (actual time=2242.074..2763.504 rows=38426 loops=1)
   Group Key: trains.train_id, wagons.wagon_nr
   ->  Sort  (cost=134154.28..136405.46 rows=900472 width=40) (actual time=2242.048..2612.513 rows=900472 loops=1)
         Sort Key: trains.train_id, wagons.wagon_nr
         Sort Method: external merge  Disk: 44120kB
         ->  Hash Join  (cost=1872.71..20473.65 rows=900472 width=40) (actual time=43.738..759.302 rows=900472 loops=1)
               Hash Cond: ((wagons.train)::text = (trains.train_id)::text)
               ->  Hash Join  (cost=1235.71..17472.41 rows=900472 width=40) (actual time=25.028..451.436 rows=900472 loops=1)
                     Hash Cond: (seats.wagon = wagons.wagon_id)
                     ->  Seq Scan on seats  (cost=0.00..13872.72 rows=900472 width=8) (actual time=0.036..137.206 rows=900472 loops=1)
                     ->  Hash  (cost=734.76..734.76 rows=40076 width=40) (actual time=24.673..24.673 rows=40076 loops=1)
                           Buckets: 65536  Batches: 1  Memory Usage: 3291kB
                           ->  Seq Scan on wagons  (cost=0.00..734.76 rows=40076 width=40) (actual time=0.009..10.156 rows=40076 loops=1)
               ->  Hash  (cost=387.00..387.00 rows=20000 width=32) (actual time=18.118..18.118 rows=20000 loops=1)
                     Buckets: 32768  Batches: 1  Memory Usage: 1487kB
                     ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=32) (actual time=0.266..6.261 rows=20000 loops=1)
 Planning time: 0.818 ms
 Execution time: 2773.085 ms
```
