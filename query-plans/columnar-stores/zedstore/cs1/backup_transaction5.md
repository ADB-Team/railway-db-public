# Main Query

```sql
2021-01-07 13:44:48.448 UTC [969] LOG:  duration: 4344.355 ms  plan:
	Query Text: /*
	* Select stations with random criteria.
	*
	* EXEC SCRIPT:
	psql [DB-NAME] < transactions/backup_transaction5.sql
	*/
	SELECT stations.station_name, date_created, nr_platforms, distance
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
		WHERE country_name = 'United States'
	)
	AND nr_platforms > 5
	AND (EXTRACT(year FROM date_created)) > 1950
	AND distance > 1000;
	Nested Loop  (cost=378.96..1684.10 rows=3 width=44)
	  InitPlan 1 (returns $0)
	    ->  Seq Scan on countries countries_1  (cost=0.00..4.79 rows=1 width=4)
	          Filter: ((country_name)::text = 'United States'::text)
	  ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=4)
	        Filter: (country_id = $0)
	  ->  Nested Loop  (cost=374.18..1674.49 rows=3 width=48)
	        Join Filter: ((routes.start_station = stations.station_id) OR (routes.end_station = stations.station_id))
	        ->  Seq Scan on routes  (cost=0.00..178.12 rows=3230 width=12)
	              Filter: (distance > 1000)
	        ->  Materialize  (cost=374.18..931.14 rows=10 width=48)
	              ->  Hash Join  (cost=374.18..931.09 rows=10 width=48)
	                    Hash Cond: (stations.city = cities.city_id)
	                    ->  Seq Scan on stations  (cost=0.00..551.14 rows=2201 width=48)
	                          Filter: ((nr_platforms > 5) AND (date_part('year'::text, (date_created)::timestamp without time zone) > '1950'::double precision))
	                    ->  Hash  (cost=373.00..373.00 rows=94 width=8)
	                          ->  Seq Scan on cities  (cost=0.00..373.00 rows=94 width=8)
	                                Filter: (country = $0)
```