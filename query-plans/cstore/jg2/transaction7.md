# Main Query

```sql
Query Text: /*
	* All trains produced in country X are out of order.
	* → Change train states to out_of_order, cancel connections with out_of_order.
	*
	* SCRIPT PARAMS:
	* country: the country, varchar
	*
	* EXEC SCRIPT:
	* psql -v name="'NAME'" -v country="'COUNTRY'" railway-system < transactions/transaction7.sql
	*
	* TEST VALUES:
	psql -v country="'Germany'" [DB-NAME] < transactions/transaction7.sql
	*/
```
	
## Update trains to out of order

```sql
	UPDATE trains SET train_state = 'out_of_order'
	WHERE producer IN (
		SELECT producer_id
		FROM producers
		JOIN cities_countries_jg ON city = city_id
		WHERE cities_countries_jg.country_name = 'Germany'
	);
	Update on trains  (cost=950.02..1426.13 rows=10000 width=127)
	  ->  Hash Join  (cost=950.02..1426.13 rows=10000 width=127)
	        Hash Cond: (trains.producer = producers.producer_id)
	        ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=50)
	        ->  Hash  (cost=932.38..932.38 rows=1411 width=77)
	              Buckets: 2048  Batches: 1  Memory Usage: 174kB
	              ->  HashAggregate  (cost=918.27..932.38 rows=1411 width=77)
	                    Group Key: producers.producer_id
	                    ->  Hash Join  (cost=321.64..914.75 rows=1411 width=77)
	                          Hash Cond: (producers.city = cities_countries_jg.city_id)
	                          ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14)
	                          ->  Hash  (cost=304.00..304.00 rows=1411 width=71)
	                                Buckets: 2048  Batches: 1  Memory Usage: 163kB
	                                ->  Foreign Scan on cities_countries_jg  (cost=0.00..304.00 rows=1411 width=71)
	                                      Filter: ((country_name)::text = 'Germany'::text)
	                                      CStore File: /var/lib/postgres/data/cstore_fdw/27870/28094
	                                      CStore File Size: 439907
```
	                                            
## Delete connections

### Delete from passengers_schedule, for every connection with this train:

```sql
Query Text: DELETE FROM ONLY "public"."passengers_schedule" WHERE $1 OPERATOR(pg_catalog.=) "connection"
	Delete on passengers_schedule  (cost=0.00..752.60 rows=216 width=6)
	  ->  Seq Scan on passengers_schedule  (cost=0.00..752.60 rows=216 width=6)
	        Filter: (72 = connection)

Query Text: DELETE FROM schedule
	WHERE train IN (
		SELECT train_id FROM trains
		JOIN producers ON producer = producer_id
		JOIN cities_countries_jg ON city = city_id
		WHERE train_state = 'out_of_order'
		AND cities_countries_jg.country_name = 'Germany'
	);
	Delete on schedule  (cost=1413.74..1884.49 rows=9770 width=85)
	  ->  Hash Join  (cost=1413.74..1884.49 rows=9770 width=85)
	        Hash Cond: ((schedule.train)::text = (trains.train_id)::text)
	        ->  Seq Scan on schedule  (cost=0.00..397.41 rows=19541 width=38)
	        ->  Hash  (cost=1413.64..1413.64 rows=8 width=111)
	              Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 220kB
	              ->  HashAggregate  (cost=1413.56..1413.64 rows=8 width=111)
	                    Group Key: (trains.train_id)::text
	                    ->  Hash Join  (cost=321.93..1413.54 rows=8 width=111)
	                          Hash Cond: (producers.city = cities_countries_jg.city_id)
	                          ->  Nested Loop  (cost=0.29..1091.42 rows=107 width=48)
	                                ->  Seq Scan on trains  (cost=0.00..469.71 rows=107 width=42)
	                                      Filter: (train_state = 'out_of_order'::train_state)
	                                ->  Index Scan using producers_pkey on producers  (cost=0.29..5.80 rows=1 width=14)
	                                      Index Cond: (producer_id = trains.producer)
	                          ->  Hash  (cost=304.00..304.00 rows=1411 width=71)
	                                Buckets: 2048  Batches: 1  Memory Usage: 163kB
	                                ->  Foreign Scan on cities_countries_jg  (cost=0.00..304.00 rows=1411 width=71)
	                                      Filter: ((country_name)::text = 'Germany'::text)
	                                      CStore File: /var/lib/postgres/data/cstore_fdw/27870/28094
	                                      CStore File Size: 439907
```
