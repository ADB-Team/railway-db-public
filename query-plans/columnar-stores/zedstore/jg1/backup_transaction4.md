# Main Query

```sql
2021-01-08 14:35:02.594 UTC [936] LOG:  duration: 291.132 ms  plan:
	Query Text: /*
	* Show number of seats per wagon in each train.
	* → Count nr of seats, group by wagon and train.
	*
	* EXEC SCRIPT:
	psql [DB-NAME] < transactions/zedstore/backup_transaction4_jg1.sql
	*/
	SELECT COUNT(seat_id), wagon_nr, train_id
	FROM seats_wagons_trains_jg
	GROUP BY train_id, wagon_nr;
	Finalize GroupAggregate  (cost=64024.49..74358.48 rows=40000 width=44)
	  Group Key: train_id, wagon_nr
	  ->  Gather Merge  (cost=64024.49..73358.48 rows=80000 width=44)
	        Workers Planned: 2
	        ->  Sort  (cost=63024.47..63124.47 rows=40000 width=44)
	              Sort Key: train_id, wagon_nr
	              ->  Partial HashAggregate  (cost=53512.21..59966.92 rows=40000 width=44)
	                    Group Key: train_id, wagon_nr
	                    ->  Parallel Seq Scan on seats_wagons_trains_jg  (cost=0.00..18637.03 rows=387502 width=40)
```