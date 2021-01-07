```sql
Query Text: /*
	* Show number of seats per wagon in each train.
	* → Count nr of seats, group by wagon and train.
	*
	* EXEC SCRIPT:
	psql [DB-NAME] < transactions/backup_transaction4.sql
	*/
	SELECT COUNT(seat_id), wagon_nr, train_id
	FROM seats JOIN wagons ON wagon = wagon_id
	JOIN trains ON train = train_id
	GROUP BY train_id, wagon_nr;
	Finalize GroupAggregate  (cost=66255.81..155367.39 rows=320000 width=43)
	  Group Key: trains.train_id, wagons.wagon_nr
	  ->  Gather Merge  (cost=66255.81..147367.39 rows=640000 width=43)
	        Workers Planned: 2
	        ->  Partial GroupAggregate  (cost=65255.79..72495.49 rows=320000 width=43)
	              Group Key: trains.train_id, wagons.wagon_nr
	              ->  Sort  (cost=65255.79..66265.71 rows=403970 width=39)
	                    Sort Key: trains.train_id, wagons.wagon_nr
	                    ->  Hash Join  (cost=1872.71..16588.83 rows=403970 width=39)
	                          Hash Cond: ((wagons.train)::text = (trains.train_id)::text)
	                          ->  Hash Join  (cost=1235.71..14891.18 rows=403970 width=39)
	                                Hash Cond: (seats_1_112560.wagon = wagons.wagon_id)
	                                ->  Parallel Append  (cost=0.00..12594.95 rows=403968 width=8)
	                                      ->  Parallel Seq Scan on seats_1_112560  (cost=0.00..1321.89 rows=71289 width=8)
	                                      ->  Parallel Seq Scan on seats_112560_225119  (cost=0.00..1321.89 rows=71289 width=8)
	                                      ->  Parallel Seq Scan on seats_225119_337678  (cost=0.00..1321.89 rows=71289 width=8)
	                                      ->  Parallel Seq Scan on seats_337678_450237  (cost=0.00..1321.89 rows=71289 width=8)
	                                      ->  Parallel Seq Scan on seats_450237_562796  (cost=0.00..1321.89 rows=71289 width=8)
	                                      ->  Parallel Seq Scan on seats_562796_675355  (cost=0.00..1321.89 rows=71289 width=8)
	                                      ->  Parallel Seq Scan on seats_675355_787914  (cost=0.00..1321.89 rows=71289 width=8)
	                                      ->  Parallel Seq Scan on seats_787914_900473  (cost=0.00..1321.89 rows=71289 width=8)
	                                ->  Hash  (cost=734.76..734.76 rows=40076 width=39)
	                                      Buckets: 65536  Batches: 1  Memory Usage: 3291kB
	                                      ->  Seq Scan on wagons  (cost=0.00..734.76 rows=40076 width=39)
	                          ->  Hash  (cost=387.00..387.00 rows=20000 width=31)
	                                Buckets: 32768  Batches: 1  Memory Usage: 1487kB
	                                ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=31)
	JIT:
	  Functions: 40
	  Options: Inlining false, Optimization false, Expressions true, Deforming true
```
