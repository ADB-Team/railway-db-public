```sql
Query Text: /*
	* Select stations with random criteria.
	*
	* EXEC SCRIPT:
	psql [DB-NAME] < transactions/backup_transaction5.sql
	*/
	SELECT stations.name, date_created, nr_platforms, distance
	FROM stations JOIN cities ON city = city_id
	JOIN countries ON country = country_id
	CROSS JOIN routes
	WHERE (
		start_station = station_id
		OR end_station = station_id
	)
	AND country = (
		SELECT country_id
		FROM countries
		WHERE name = 'United States'
	)
	AND nr_platforms > 5
	AND (EXTRACT(year FROM date_created)) > 1950
	AND distance > 1000;
	Nested Loop  (cost=379.04..2759.13 rows=8 width=44)
	  InitPlan 1 (returns $0)
	    ->  Seq Scan on countries countries_1  (cost=0.00..4.79 rows=1 width=4)
	          Filter: ((name)::text = 'United States'::text)
	  ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=4)
	        Filter: (country_id = $0)
	  ->  Nested Loop  (cost=374.25..2749.48 rows=8 width=48)
	        Join Filter: ((routes_0.start_station = stations.station_id) OR (routes_0.end_station = stations.station_id))
	        ->  Append  (cost=0.00..432.86 rows=7197 width=12)
	              ->  Seq Scan on routes_0  (cost=0.00..78.12 rows=1417 width=12)
	                    Filter: (distance > 1000)
	              ->  Seq Scan on routes_1  (cost=0.00..81.25 rows=1473 width=12)
	                    Filter: (distance > 1000)
	              ->  Seq Scan on routes_2  (cost=0.00..78.12 rows=1417 width=12)
	                    Filter: (distance > 1000)
	              ->  Seq Scan on routes_3  (cost=0.00..78.12 rows=1417 width=12)
	                    Filter: (distance > 1000)
	              ->  Seq Scan on routes_4  (cost=0.00..81.25 rows=1473 width=12)
	                    Filter: (distance > 1000)
	        ->  Materialize  (cost=374.25..931.22 rows=11 width=48)
	              ->  Hash Join  (cost=374.25..931.17 rows=11 width=48)
	                    Hash Cond: (stations.city = cities.city_id)
	                    ->  Seq Scan on stations  (cost=0.00..551.14 rows=2201 width=48)
	                          Filter: ((nr_platforms > 5) AND (date_part('year'::text, (date_created)::timestamp without time zone) > '1950'::double precision))
	                    ->  Hash  (cost=373.00..373.00 rows=100 width=8)
	                          Buckets: 8192 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 226kB
	                          ->  Seq Scan on cities  (cost=0.00..373.00 rows=100 width=8)
	                                Filter: (country = $0)
```
