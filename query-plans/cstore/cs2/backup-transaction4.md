## MAIN QUERY
```sql
Query Text: /*
* Show number of seats per wagon in each train.
* → Count nr of seats, group by wagon and train.
*
* EXEC SCRIPT:
psql [DB-NAME] < transactions/backup_transaction4.sql
*/
GroupAggregate  (cost=129556.28..147565.72 rows=900472 width=44) (actual time=1903.342..2566.841 rows=38426 loops=1)
   Group Key: trains.train_id, wagons.wagon_nr
   ->  Sort  (cost=129556.28..131807.46 rows=900472 width=40) (actual time=1903.285..2373.951 rows=900472 loops=1)
         Sort Key: trains.train_id, wagons.wagon_nr
         Sort Method: external merge  Disk: 44120kB
         ->  Hash Join  (cost=1872.71..15875.65 rows=900472 width=40) (actual time=27.956..656.325 rows=900472 loops=1)
               Hash Cond: ((wagons.train)::text = (trains.train_id)::text)
               ->  Hash Join  (cost=1235.71..12874.41 rows=900472 width=40) (actual time=18.346..383.262 rows=900472 loops=1)
                     Hash Cond: (seats_cs.wagon = wagons.wagon_id)
                     ->  Foreign Scan on seats_cs  (cost=0.00..9274.72 rows=900472 width=8) (actual time=0.736..126.718 rows=900472 loops=1)
                           CStore File: /var/lib/postgres/data/cstore_fdw/66101/66321
                           CStore File Size: 4419425
                     ->  Hash  (cost=734.76..734.76 rows=40076 width=40) (actual time=17.398..17.398 rows=40076 loops=1)
                           Buckets: 65536  Batches: 1  Memory Usage: 3291kB
                           ->  Seq Scan on wagons  (cost=0.00..734.76 rows=40076 width=40) (actual time=0.006..7.117 rows=40076 loops=1)
               ->  Hash  (cost=387.00..387.00 rows=20000 width=32) (actual time=9.487..9.487 rows=20000 loops=1)
                     Buckets: 32768  Batches: 1  Memory Usage: 1487kB
                     ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=32) (actual time=0.014..3.184 rows=20000 loops=1)
 Planning time: 0.688 ms
 Execution time: 2585.009 ms
(20 filas)

```
