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
		JOIN cities ON city = city_id
		JOIN countries ON country = country_id
		WHERE countries.name = 'Germany'
	);
	Update on trains  (cost=963.45..1403.95 rows=90 width=72)
	  ->  Hash Semi Join  (cost=963.45..1403.95 rows=90 width=72)
	        Hash Cond: (trains.producer = producers.producer_id)
	        ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=50)
	        ->  Hash  (cost=962.33..962.33 rows=90 width=22)
	              Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
	              ->  Hash Join  (cost=382.43..962.33 rows=90 width=22)
	                    Hash Cond: (producers.city = cities.city_id)
	                    ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14)
	                    ->  Hash  (cost=381.30..381.30 rows=90 width=16)
	                          Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
	                          ->  Hash Join  (cost=4.80..381.30 rows=90 width=16)
	                                Hash Cond: (cities.country = countries.country_id)
	                                ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14)
	                                ->  Hash  (cost=4.79..4.79 rows=1 width=10)
	                                      Buckets: 1024  Batches: 1  Memory Usage: 9kB
	                                      ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10)
	                                            Filter: ((name)::text = 'Germany'::text)
```
	                                            
## Delete connections

### Delete from passengers_schedule, for every connection with this train:

```sql
Query Text: DELETE FROM "public"."passengers_schedule" WHERE $1 OPERATOR(pg_catalog.=) "connection"
	Delete on passengers_schedule  (cost=0.00..386.95 rows=109 width=6)
	  Delete on passengers_schedule_0
	  Delete on passengers_schedule_1
	  Delete on passengers_schedule_2
	  Delete on passengers_schedule_3
	  Delete on passengers_schedule_4
	  ->  Seq Scan on passengers_schedule_0  (cost=0.00..78.10 rows=22 width=6)
	        Filter: (72 = connection)
	  ->  Seq Scan on passengers_schedule_1  (cost=0.00..78.10 rows=22 width=6)
	        Filter: (72 = connection)
	  ->  Seq Scan on passengers_schedule_2  (cost=0.00..78.10 rows=22 width=6)
	        Filter: (72 = connection)
	  ->  Seq Scan on passengers_schedule_3  (cost=0.00..74.55 rows=21 width=6)
	        Filter: (72 = connection)
	  ->  Seq Scan on passengers_schedule_4  (cost=0.00..78.10 rows=22 width=6)
	        Filter: (72 = connection)

Query Text: DELETE FROM schedule
	WHERE train IN (
		SELECT train_id FROM trains
		JOIN producers ON producer = producer_id
		JOIN cities ON city = city_id
		JOIN countries ON country = country_id
		WHERE train_state = 'out_of_order'
		AND countries.name = 'Germany'
	);
	Delete on schedule  (cost=1132.97..1581.69 rows=1 width=30)
	  ->  Hash Semi Join  (cost=1132.97..1581.69 rows=1 width=30)
	        Hash Cond: ((schedule.train)::text = (trains.train_id)::text)
	        ->  Seq Scan on schedule  (cost=0.00..397.41 rows=19541 width=37)
	        ->  Hash  (cost=1132.96..1132.96 rows=1 width=56)
	              Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 141kB
	              ->  Nested Loop  (cost=0.57..1132.96 rows=1 width=56)
	                    Join Filter: (cities.country = countries.country_id)
	                    ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10)
	                          Filter: ((name)::text = 'Germany'::text)
	                    ->  Nested Loop  (cost=0.57..1126.84 rows=107 width=54)
	                          ->  Nested Loop  (cost=0.29..1090.35 rows=107 width=48)
	                                ->  Seq Scan on trains  (cost=0.00..469.71 rows=107 width=42)
	                                      Filter: (train_state = 'out_of_order'::train_state)
	                                ->  Index Scan using producers_pkey on producers  (cost=0.29..5.80 rows=1 width=14)
	                                      Index Cond: (producer_id = trains.producer)
	                          ->  Index Scan using cities_pkey on cities  (cost=0.29..0.34 rows=1 width=14)
	                                Index Cond: (city_id = producers.city)
```
