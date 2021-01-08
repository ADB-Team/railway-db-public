# Main Query

```sql
2021-01-08 14:45:10.538 UTC [1216] LOG:  duration: 80.071 ms  plan:
	Query Text: /*
	* Select stations with random criteria.
	*
	* EXEC SCRIPT:
	psql [DB-NAME] < transactions/zedstore/backup_transaction5.sql
	*/
	SELECT stations.station_name, date_created, nr_platforms, distance
	FROM stations JOIN cities_countries_jg ON city = city_id
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
	Hash Join  (cost=376.30..31548.32 rows=493 width=44)
	  Hash Cond: (stations.city = cities_countries_jg.city_id)
	  InitPlan 1 (returns $0)
	    ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=4)
	          Filter: ((country_name)::text = 'United States'::text)
	  ->  Nested Loop  (cost=0.66..31151.45 rows=1449 width=48)
	        ->  Seq Scan on routes  (cost=0.00..369.54 rows=6521 width=12)
	              Filter: (distance > 1000)
	        ->  Bitmap Heap Scan on stations  (cost=0.66..4.71 rows=1 width=48)
	              Recheck Cond: ((routes.start_station = station_id) OR (routes.end_station = station_id))
	              Filter: ((nr_platforms > 5) AND (date_part('year'::text, (date_created)::timestamp without time zone) > '1950'::double precision))
	              ->  BitmapOr  (cost=0.66..0.66 rows=2 width=0)
	                    ->  Bitmap Index Scan on stations_pkey  (cost=0.00..0.33 rows=1 width=0)
	                          Index Cond: (station_id = routes.start_station)
	                    ->  Bitmap Index Scan on stations_pkey  (cost=0.00..0.33 rows=1 width=0)
	                          Index Cond: (station_id = routes.end_station)
	  ->  Hash  (cost=370.00..370.00 rows=68 width=4)
	        ->  Seq Scan on cities_countries_jg  (cost=0.00..370.00 rows=68 width=4)
	              Filter: (country = $0)
```
