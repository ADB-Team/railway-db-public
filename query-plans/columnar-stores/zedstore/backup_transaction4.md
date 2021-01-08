# Main Query

```sql
2021-01-07 13:44:04.980 UTC [943] LOG:  duration: 963.986 ms  plan:
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
	HashAggregate  (cost=101516.13..124590.73 rows=900472 width=43)
	  Group Key: trains.train_id, wagons.wagon_nr
	  Planned Partitions: 32
	  ->  Hash Join  (cost=1872.71..20473.65 rows=900472 width=39)
	        Hash Cond: ((wagons.train)::text = (trains.train_id)::text)
	        ->  Hash Join  (cost=1235.71..17472.41 rows=900472 width=40)
	              Hash Cond: (seats.wagon = wagons.wagon_id)
	              ->  Seq Scan on seats  (cost=0.00..13872.72 rows=900472 width=8)
	              ->  Hash  (cost=734.76..734.76 rows=40076 width=40)
	                    ->  Seq Scan on wagons  (cost=0.00..734.76 rows=40076 width=40)
	        ->  Hash  (cost=387.00..387.00 rows=20000 width=31)
	              ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=31)
```