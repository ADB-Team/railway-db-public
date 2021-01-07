# Creating stations

```sql
Query Text: SELECT 1 FROM ONLY "public"."cities" x WHERE "city_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using cities_pkey on cities x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (city_id = 9482)
2021-01-07 13:27:05.893 UTC [749] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."cities" x WHERE "city_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-07 13:27:05.893 UTC [749] LOG:  duration: 6.539 ms  plan:
	Query Text: /*
	* My dream comes true and Görlitz finally gets a regular connection to Berlin.
	* → Add new connection for every hour for one year from today on.
	*
	* SCRIPT PARAMS:
	* train_id: code of the train, varchar
	*
	* EXEC SCRIPT:
	* psql -v start_station="'START'" -v end_station="'END'" -v start_time="'DATE TIME'" -v train_id="'TRAIN'" [DB-NAME] < transactions/transaction3.sql
	*
	* TEST VALUES:
	psql -v train_id="'bywdfevctetzjdwvofemzctyerwshr'" [DB-NAME] < transactions/transaction3.sql
	*/
	INSERT INTO stations (station_name, nr_platforms, city, date_created) VALUES (
		'Görlitz HBF', 12, (
			SELECT city_id FROM cities WHERE city_name = 'Görlitz'
		), now()::date
	);
	Insert on stations  (cost=373.00..373.02 rows=0 width=0)
	  InitPlan 1 (returns $0)
	    ->  Seq Scan on cities  (cost=0.00..373.00 rows=100 width=4)
	          Filter: ((city_name)::text = 'Görlitz'::text)
	  ->  Result  (cost=0.00..0.02 rows=1 width=48)
2021-01-07 13:27:05.902 UTC [749] LOG:  duration: 0.085 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."cities" x WHERE "city_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using cities_pkey on cities x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (city_id = 10544)
2021-01-07 13:27:05.902 UTC [749] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."cities" x WHERE "city_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-07 13:27:05.902 UTC [749] LOG:  duration: 4.714 ms  plan:
	Query Text: INSERT INTO stations (station_name, nr_platforms, city, date_created) VALUES (
		'Berlin Görlitzer Bhf', 6, (
			SELECT city_id FROM cities WHERE city_name LIKE '%Berlin%'
		), '1940-03-14'
	);
	Insert on stations  (cost=373.00..373.01 rows=0 width=0)
	  InitPlan 1 (returns $0)
	    ->  Seq Scan on cities  (cost=0.00..373.00 rows=6 width=4)
	          Filter: ((city_name)::text ~~ '%Berlin%'::text)
	  ->  Result  (cost=0.00..0.01 rows=1 width=48)
```

# Select those same stations for further queries

```sql
2021-01-07 13:27:05.919 UTC [749] LOG:  duration: 0.075 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 20001)
2021-01-07 13:27:05.919 UTC [749] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-07 13:27:05.920 UTC [749] LOG:  duration: 0.020 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 20002)
2021-01-07 13:27:05.920 UTC [749] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-07 13:27:05.920 UTC [749] LOG:  duration: 0.018 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 20002)
2021-01-07 13:27:05.920 UTC [749] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-07 13:27:05.920 UTC [749] LOG:  duration: 0.021 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 20001)
2021-01-07 13:27:05.920 UTC [749] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x" 
```

# Insert new routes with new stations

```sql
2021-01-07 13:27:05.920 UTC [749] LOG:  duration: 13.146 ms  plan:
	Query Text: INSERT INTO routes (start_station, end_station, distance, max_speed) VALUES
	((
			SELECT station_id FROM stations WHERE station_name = 'Görlitz HBF'
		), (
			SELECT station_id FROM stations WHERE station_name = 'Berlin Görlitzer Bhf'
		), 220, 300
	),
	((
			SELECT station_id FROM stations WHERE station_name = 'Berlin Görlitzer Bhf'
		), (
			SELECT station_id FROM stations WHERE station_name = 'Görlitz HBF'
		), 220, 300
	);
	Insert on routes  (cost=1610.35..1610.38 rows=0 width=0)
	  InitPlan 1 (returns $0)
	    ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=4)
	          Filter: ((station_name)::text = 'Görlitz HBF'::text)
	  InitPlan 2 (returns $1)
	    ->  Seq Scan on stations stations_1  (cost=0.00..402.59 rows=99 width=4)
	          Filter: ((station_name)::text = 'Berlin Görlitzer Bhf'::text)
	  InitPlan 3 (returns $2)
	    ->  Seq Scan on stations stations_2  (cost=0.00..402.59 rows=99 width=4)
	          Filter: ((station_name)::text = 'Berlin Görlitzer Bhf'::text)
	  InitPlan 4 (returns $3)
	    ->  Seq Scan on stations stations_3  (cost=0.00..402.59 rows=99 width=4)
	          Filter: ((station_name)::text = 'Görlitz HBF'::text)
	  ->  Values Scan on "*VALUES*"  (cost=0.00..0.04 rows=2 width=20)
```

# Generate and insert connections with those routes

Calculate arrival times for each generated beginning time of each connection:

```sql
2021-01-07 13:27:05.941 UTC [749] LOG:  duration: 0.048 ms  plan:
	Query Text: SELECT $1 - (SELECT floor(random()*($1 / 4 - 0))+10)
	Result  (cost=0.02..0.03 rows=1 width=8)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.02 rows=1 width=8)
2021-01-07 13:27:05.941 UTC [749] CONTEXT:  SQL statement "SELECT $1 - (SELECT floor(random()*($1 / 4 - 0))+10)"
	PL/pgSQL function calc_avg_speed(numeric,numeric) line 4 at RETURN
	SQL statement "SELECT $1 + (
				SELECT calc_duration ((
					SELECT calc_avg_speed($2, $4)
				), $3))"
	PL/pgSQL function calc_end_time(timestamp without time zone,integer,integer,integer) line 3 at RETURN
2021-01-07 13:27:05.941 UTC [749] LOG:  duration: 0.002 ms  plan:
	Query Text: SELECT (SELECT result * interval '1 hour')
	Result  (cost=0.01..0.02 rows=1 width=16)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.01 rows=1 width=16)
2021-01-07 13:27:05.941 UTC [749] CONTEXT:  SQL statement "SELECT (SELECT result * interval '1 hour')"
	PL/pgSQL function calc_duration(numeric,numeric) line 5 at RETURN
	SQL statement "SELECT $1 + (
				SELECT calc_duration ((
					SELECT calc_avg_speed($2, $4)
				), $3))"
	PL/pgSQL function calc_end_time(timestamp without time zone,integer,integer,integer) line 3 at RETURN
2021-01-07 13:27:05.941 UTC [749] LOG:  duration: 0.864 ms  plan:
	Query Text: SELECT $1 + (
				SELECT calc_duration ((
					SELECT calc_avg_speed($2, $4)
				), $3))
	Result  (cost=0.52..0.53 rows=1 width=8)
	  InitPlan 2 (returns $1)
	    ->  Result  (cost=0.26..0.52 rows=1 width=16)
	          InitPlan 1 (returns $0)
	            ->  Result  (cost=0.00..0.26 rows=1 width=4)
2021-01-07 13:27:05.941 UTC [749] CONTEXT:  SQL statement "SELECT $1 + (
				SELECT calc_duration ((
					SELECT calc_avg_speed($2, $4)
				), $3))"
	PL/pgSQL function calc_end_time(timestamp without time zone,integer,integer,integer) line 3 at RETURN
```

Select route and train for every connection:

```sql
2021-01-07 13:27:07.411 UTC [749] LOG:  duration: 0.033 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."routes_rs" x WHERE "route_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using routes_pkey on routes_rs x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (route_id = 19564)
2021-01-07 13:27:07.411 UTC [749] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."routes_rs" x WHERE "route_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-07 13:27:07.411 UTC [749] LOG:  duration: 0.017 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."trains" x WHERE "train_id"::pg_catalog.text OPERATOR(pg_catalog.=) $1::pg_catalog.text FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using trains_pkey on trains x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: ((train_id)::text = 'bywdfevctetzjdwvofemzctyerwshr'::text)
2021-01-07 13:27:07.411 UTC [749] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."trains" x WHERE "train_id"::pg_catalog.text OPERATOR(pg_catalog.=) $1::pg_catalog.text FOR KEY SHARE OF x"
```

Insert connections:

```sql
2021-01-07 13:27:08.461 UTC [749] LOG:  duration: 2536.930 ms  plan:
	Query Text: WITH selected_routes AS (
		SELECT * FROM routes
		WHERE start_station = (
			SELECT station_id FROM stations WHERE station_name = 'Görlitz HBF'
		) OR start_station = (
			SELECT station_id FROM stations WHERE station_name = 'Berlin Görlitzer Bhf'
		) OR end_station = (
			SELECT station_id FROM stations WHERE station_name = 'Görlitz HBF'
		) OR end_station = (
			SELECT station_id FROM stations WHERE station_name = 'Berlin Görlitzer Bhf'
		)
	),
	train AS(
		SELECT * FROM trains
		WHERE train_id = 'bywdfevctetzjdwvofemzctyerwshr'
	),
	starting_times AS (
		SELECT start_time
		FROM  generate_series(
			now(), now() + interval '1 year', interval '1 hour'
		) AS start_time
	)
	INSERT INTO schedule (route, train, start_time, end_time)
	(
		SELECT route_id, train_id, start_time, ending_times.calc
		FROM selected_routes AS sr1
		CROSS JOIN train
		CROSS JOIN starting_times
		CROSS JOIN LATERAL (
			SELECT calc_end_time(start_time::timestamp, (
				SELECT max_speed FROM train_types JOIN train ON train_type = train_type_id
			), (
				SELECT distance FROM selected_routes AS sr2 WHERE sr1.route_id = sr2.route_id
			), (
				SELECT max_speed FROM selected_routes AS sr3 WHERE sr1.route_id = sr3.route_id
			)) AS calc
		) AS ending_times
	);
	Insert on schedule  (cost=1880.52..2183005.73 rows=0 width=0)
	  CTE selected_routes
	    ->  Seq Scan on routes  (cost=1610.35..1861.15 rows=192 width=20)
	          Filter: ((start_station = $0) OR (start_station = $1) OR (end_station = $2) OR (end_station = $3))
	          InitPlan 1 (returns $0)
	            ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=4)
	                  Filter: ((station_name)::text = 'Görlitz HBF'::text)
	          InitPlan 2 (returns $1)
	            ->  Seq Scan on stations stations_1  (cost=0.00..402.59 rows=99 width=4)
	                  Filter: ((station_name)::text = 'Berlin Görlitzer Bhf'::text)
	          InitPlan 3 (returns $2)
	            ->  Seq Scan on stations stations_2  (cost=0.00..402.59 rows=99 width=4)
	                  Filter: ((station_name)::text = 'Görlitz HBF'::text)
	          InitPlan 4 (returns $3)
	            ->  Seq Scan on stations stations_3  (cost=0.00..402.59 rows=99 width=4)
	                  Filter: ((station_name)::text = 'Berlin Görlitzer Bhf'::text)
	  CTE train
	    ->  Index Scan using trains_pkey on trains  (cost=0.29..8.30 rows=1 width=48)
	          Index Cond: ((train_id)::text = 'bywdfevctetzjdwvofemzctyerwshr'::text)
	  ->  Nested Loop  (cost=11.07..2181136.27 rows=192000 width=56)
	        ->  Nested Loop  (cost=0.01..2416.27 rows=192000 width=44)
	              ->  Function Scan on generate_series start_time  (cost=0.01..10.01 rows=1000 width=8)
	              ->  Materialize  (cost=0.00..6.74 rows=192 width=36)
	                    ->  Nested Loop  (cost=0.00..5.78 rows=192 width=36)
	                          ->  CTE Scan on train  (cost=0.00..0.02 rows=1 width=32)
	                          ->  CTE Scan on selected_routes sr1  (cost=0.00..3.84 rows=192 width=4)
	        ->  Result  (cost=11.06..11.32 rows=1 width=8)
	              InitPlan 7 (returns $6)
	                ->  Hash Join  (cost=0.03..2.42 rows=1 width=4)
	                      Hash Cond: (train_types.train_type_id = train_1.train_type)
	                      ->  Seq Scan on train_types  (cost=0.00..2.00 rows=100 width=8)
	                      ->  Hash  (cost=0.02..0.02 rows=1 width=4)
	                            ->  CTE Scan on train train_1  (cost=0.00..0.02 rows=1 width=4)
	              InitPlan 8 (returns $8)
	                ->  CTE Scan on selected_routes sr2  (cost=0.00..4.32 rows=1 width=4)
	                      Filter: ($7 = route_id)
	              InitPlan 9 (returns $9)
	                ->  CTE Scan on selected_routes sr3  (cost=0.00..4.32 rows=1 width=4)
	                      Filter: ($7 = route_id)
```

Update catalog:

```sql
2021-01-07 13:32:46.541 UTC [800] LOG:  duration: 0.037 ms  plan:
	Query Text: SELECT pg_catalog.set_config('search_path', '', false);
	Result  (cost=0.00..0.01 rows=1 width=32)
2021-01-07 13:32:46.610 UTC [802] LOG:  duration: 0.011 ms  plan:
	Query Text: SELECT pg_catalog.set_config('search_path', '', false);
	Result  (cost=0.00..0.01 rows=1 width=32)
2021-01-07 13:32:46.733 UTC [804] LOG:  duration: 0.011 ms  plan:
	Query Text: SELECT pg_catalog.set_config('search_path', '', false);
	Result  (cost=0.00..0.01 rows=1 width=32)
2021-01-07 13:32:48.038 UTC [804] LOG:  duration: 0.034 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.cities_city_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.039 UTC [804] LOG:  duration: 0.044 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.countries_country_id_seq', 223, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.041 UTC [804] LOG:  duration: 0.059 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.passengers_passenger_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.042 UTC [804] LOG:  duration: 0.030 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.producers_producer_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.043 UTC [804] LOG:  duration: 0.031 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.routes_route_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.046 UTC [804] LOG:  duration: 0.126 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.schedule_connection_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.047 UTC [804] LOG:  duration: 0.034 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.seats_seat_id_seq', 900472, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.048 UTC [804] LOG:  duration: 0.030 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.stations_station_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.049 UTC [804] LOG:  duration: 0.036 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.train_types_train_type_id_seq', 100, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-07 13:32:48.050 UTC [804] LOG:  duration: 0.035 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.wagons_wagon_id_seq', 40076, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
```

Join tables together:

```sql
2021-01-07 13:32:48.809 UTC [804] LOG:  duration: 8.629 ms  plan:
	Query Text: SELECT fk."country" FROM ONLY "public"."cities" fk LEFT OUTER JOIN ONLY "public"."countries" pk ON ( pk."country_id" OPERATOR(pg_catalog.=) fk."country") WHERE pk."country_id" IS NULL AND (fk."country" IS NOT NULL)
	Hash Anti Join  (cost=7.02..492.95 rows=1 width=4)
	  Hash Cond: (fk.country = pk.country_id)
	  ->  Seq Scan on cities fk  (cost=0.00..323.00 rows=19900 width=4)
	        Filter: (country IS NOT NULL)
	  ->  Hash  (cost=4.23..4.23 rows=223 width=4)
	        ->  Seq Scan on countries pk  (cost=0.00..4.23 rows=223 width=4)
2021-01-07 13:32:48.809 UTC [804] CONTEXT:  SQL statement "SELECT fk."country" FROM ONLY "public"."cities" fk LEFT OUTER JOIN ONLY "public"."countries" pk ON ( pk."country_id" OPERATOR(pg_catalog.=) fk."country") WHERE pk."country_id" IS NULL AND (fk."country" IS NOT NULL)"
2021-01-07 13:32:48.824 UTC [804] LOG:  duration: 12.110 ms  plan:
	Query Text: SELECT fk."city" FROM ONLY "public"."passengers" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)
	Hash Anti Join  (cost=573.00..1256.93 rows=1 width=4)
	  Hash Cond: (fk.city = pk.city_id)
	  ->  Seq Scan on passengers fk  (cost=0.00..521.00 rows=19900 width=4)
	        Filter: (city IS NOT NULL)
	  ->  Hash  (cost=323.00..323.00 rows=20000 width=4)
	        ->  Seq Scan on cities pk  (cost=0.00..323.00 rows=20000 width=4)
2021-01-07 13:32:48.824 UTC [804] CONTEXT:  SQL statement "SELECT fk."city" FROM ONLY "public"."passengers" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)"
2021-01-07 13:32:48.842 UTC [804] LOG:  duration: 16.302 ms  plan:
	Query Text: SELECT fk."connection" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."schedule" pk ON ( pk."connection_id" OPERATOR(pg_catalog.=) fk."connection") WHERE pk."connection_id" IS NULL AND (fk."connection" IS NOT NULL)
	Hash Anti Join  (cost=641.67..1638.48 rows=1 width=4)
	  Hash Cond: (fk.connection = pk.connection_id)
	  ->  Seq Scan on passengers_schedule fk  (cost=0.00..644.48 rows=43032 width=4)
	        Filter: (connection IS NOT NULL)
	  ->  Hash  (cost=397.41..397.41 rows=19541 width=4)
	        ->  Seq Scan on schedule pk  (cost=0.00..397.41 rows=19541 width=4)
2021-01-07 13:32:48.842 UTC [804] CONTEXT:  SQL statement "SELECT fk."connection" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."schedule" pk ON ( pk."connection_id" OPERATOR(pg_catalog.=) fk."connection") WHERE pk."connection_id" IS NULL AND (fk."connection" IS NOT NULL)"
2021-01-07 13:32:48.856 UTC [804] LOG:  duration: 12.155 ms  plan:
	Query Text: SELECT fk."passenger" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."passengers" pk ON ( pk."passenger_id" OPERATOR(pg_catalog.=) fk."passenger") WHERE pk."passenger_id" IS NULL AND (fk."passenger" IS NOT NULL)
	Hash Anti Join  (cost=771.00..1767.80 rows=1 width=4)
	  Hash Cond: (fk.passenger = pk.passenger_id)
	  ->  Seq Scan on passengers_schedule fk  (cost=0.00..644.48 rows=43032 width=4)
	        Filter: (passenger IS NOT NULL)
	  ->  Hash  (cost=521.00..521.00 rows=20000 width=4)
	        ->  Seq Scan on passengers pk  (cost=0.00..521.00 rows=20000 width=4)
2021-01-07 13:32:48.856 UTC [804] CONTEXT:  SQL statement "SELECT fk."passenger" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."passengers" pk ON ( pk."passenger_id" OPERATOR(pg_catalog.=) fk."passenger") WHERE pk."passenger_id" IS NULL AND (fk."passenger" IS NOT NULL)"
2021-01-07 13:32:49.139 UTC [804] LOG:  duration: 281.727 ms  plan:
	Query Text: SELECT fk."seat" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."seats" pk ON ( pk."seat_id" OPERATOR(pg_catalog.=) fk."seat") WHERE pk."seat_id" IS NULL AND (fk."seat" IS NOT NULL)
	Hash Anti Join  (cost=25128.62..26125.42 rows=1 width=4)
	  Hash Cond: (fk.seat = pk.seat_id)
	  ->  Seq Scan on passengers_schedule fk  (cost=0.00..644.48 rows=43032 width=4)
	        Filter: (seat IS NOT NULL)
	  ->  Hash  (cost=13872.72..13872.72 rows=900472 width=4)
	        ->  Seq Scan on seats pk  (cost=0.00..13872.72 rows=900472 width=4)
2021-01-07 13:32:49.139 UTC [804] CONTEXT:  SQL statement "SELECT fk."seat" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."seats" pk ON ( pk."seat_id" OPERATOR(pg_catalog.=) fk."seat") WHERE pk."seat_id" IS NULL AND (fk."seat" IS NOT NULL)"
2021-01-07 13:32:49.160 UTC [804] LOG:  duration: 14.520 ms  plan:
	Query Text: SELECT fk."city" FROM ONLY "public"."producers" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)
	Hash Anti Join  (cost=573.00..1239.93 rows=1 width=4)
	  Hash Cond: (fk.city = pk.city_id)
	  ->  Seq Scan on producers fk  (cost=0.00..504.00 rows=19900 width=4)
	        Filter: (city IS NOT NULL)
	  ->  Hash  (cost=323.00..323.00 rows=20000 width=4)
	        ->  Seq Scan on cities pk  (cost=0.00..323.00 rows=20000 width=4)
2021-01-07 13:32:49.160 UTC [804] CONTEXT:  SQL statement "SELECT fk."city" FROM ONLY "public"."producers" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)"
2021-01-07 13:32:49.179 UTC [804] LOG:  duration: 14.014 ms  plan:
	Query Text: SELECT fk."end_station" FROM ONLY "public"."routes" fk LEFT OUTER JOIN ONLY "public"."stations" pk ON ( pk."station_id" OPERATOR(pg_catalog.=) fk."end_station") WHERE pk."station_id" IS NULL AND (fk."end_station" IS NOT NULL)
	Hash Anti Join  (cost=600.66..1080.66 rows=1 width=4)
	  Hash Cond: (fk.end_station = pk.station_id)
	  ->  Seq Scan on routes fk  (cost=0.00..320.63 rows=19465 width=4)
	        Filter: (end_station IS NOT NULL)
	  ->  Hash  (cost=353.07..353.07 rows=19807 width=4)
	        ->  Seq Scan on stations pk  (cost=0.00..353.07 rows=19807 width=4)
2021-01-07 13:32:49.179 UTC [804] CONTEXT:  SQL statement "SELECT fk."end_station" FROM ONLY "public"."routes" fk LEFT OUTER JOIN ONLY "public"."stations" pk ON ( pk."station_id" OPERATOR(pg_catalog.=) fk."end_station") WHERE pk."station_id" IS NULL AND (fk."end_station" IS NOT NULL)"
2021-01-07 13:32:49.196 UTC [804] LOG:  duration: 15.516 ms  plan:
	Query Text: SELECT fk."start_station" FROM ONLY "public"."routes" fk LEFT OUTER JOIN ONLY "public"."stations" pk ON ( pk."station_id" OPERATOR(pg_catalog.=) fk."start_station") WHERE pk."station_id" IS NULL AND (fk."start_station" IS NOT NULL)
	Hash Anti Join  (cost=600.66..1080.66 rows=1 width=4)
	  Hash Cond: (fk.start_station = pk.station_id)
	  ->  Seq Scan on routes fk  (cost=0.00..320.63 rows=19465 width=4)
	        Filter: (start_station IS NOT NULL)
	  ->  Hash  (cost=353.07..353.07 rows=19807 width=4)
	        ->  Seq Scan on stations pk  (cost=0.00..353.07 rows=19807 width=4)
2021-01-07 13:32:49.196 UTC [804] CONTEXT:  SQL statement "SELECT fk."start_station" FROM ONLY "public"."routes" fk LEFT OUTER JOIN ONLY "public"."stations" pk ON ( pk."station_id" OPERATOR(pg_catalog.=) fk."start_station") WHERE pk."station_id" IS NULL AND (fk."start_station" IS NOT NULL)"
2021-01-07 13:32:49.206 UTC [804] LOG:  duration: 8.216 ms  plan:
	Query Text: SELECT fk."route" FROM ONLY "public"."schedule" fk LEFT OUTER JOIN ONLY "public"."routes" pk ON ( pk."route_id" OPERATOR(pg_catalog.=) fk."route") WHERE pk."route_id" IS NULL AND (fk."route" IS NOT NULL)
	Hash Anti Join  (cost=565.17..1121.76 rows=1 width=4)
	  Hash Cond: (fk.route = pk.route_id)
	  ->  Seq Scan on schedule fk  (cost=0.00..397.41 rows=19443 width=4)
	        Filter: (route IS NOT NULL)
	  ->  Hash  (cost=320.63..320.63 rows=19563 width=4)
	        ->  Seq Scan on routes pk  (cost=0.00..320.63 rows=19563 width=4)
2021-01-07 13:32:49.206 UTC [804] CONTEXT:  SQL statement "SELECT fk."route" FROM ONLY "public"."schedule" fk LEFT OUTER JOIN ONLY "public"."routes" pk ON ( pk."route_id" OPERATOR(pg_catalog.=) fk."route") WHERE pk."route_id" IS NULL AND (fk."route" IS NOT NULL)"
2021-01-07 13:32:49.222 UTC [804] LOG:  duration: 13.407 ms  plan:
	Query Text: SELECT fk."train" FROM ONLY "public"."schedule" fk LEFT OUTER JOIN ONLY "public"."trains" pk ON ( pk."train_id"::pg_catalog.text OPERATOR(pg_catalog.=) fk."train"::pg_catalog.text) WHERE pk."train_id" IS NULL AND (fk."train" IS NOT NULL)
	Hash Anti Join  (cost=637.00..1193.60 rows=1 width=32)
	  Hash Cond: ((fk.train)::text = (pk.train_id)::text)
	  ->  Seq Scan on schedule fk  (cost=0.00..397.41 rows=19443 width=32)
	        Filter: (train IS NOT NULL)
	  ->  Hash  (cost=387.00..387.00 rows=20000 width=32)
	        ->  Seq Scan on trains pk  (cost=0.00..387.00 rows=20000 width=32)
2021-01-07 13:32:49.222 UTC [804] CONTEXT:  SQL statement "SELECT fk."train" FROM ONLY "public"."schedule" fk LEFT OUTER JOIN ONLY "public"."trains" pk ON ( pk."train_id"::pg_catalog.text OPERATOR(pg_catalog.=) fk."train"::pg_catalog.text) WHERE pk."train_id" IS NULL AND (fk."train" IS NOT NULL)"
2021-01-07 13:32:49.430 UTC [804] LOG:  duration: 206.183 ms  plan:
	Query Text: SELECT fk."wagon" FROM ONLY "public"."seats" fk LEFT OUTER JOIN ONLY "public"."wagons" pk ON ( pk."wagon_id" OPERATOR(pg_catalog.=) fk."wagon") WHERE pk."wagon_id" IS NULL AND (fk."wagon" IS NOT NULL)
	Hash Anti Join  (cost=1235.71..22444.18 rows=1 width=4)
	  Hash Cond: (fk.wagon = pk.wagon_id)
	  ->  Seq Scan on seats fk  (cost=0.00..13872.72 rows=895970 width=4)
	        Filter: (wagon IS NOT NULL)
	  ->  Hash  (cost=734.76..734.76 rows=40076 width=4)
	        ->  Seq Scan on wagons pk  (cost=0.00..734.76 rows=40076 width=4)
2021-01-07 13:32:49.430 UTC [804] CONTEXT:  SQL statement "SELECT fk."wagon" FROM ONLY "public"."seats" fk LEFT OUTER JOIN ONLY "public"."wagons" pk ON ( pk."wagon_id" OPERATOR(pg_catalog.=) fk."wagon") WHERE pk."wagon_id" IS NULL AND (fk."wagon" IS NOT NULL)"
2021-01-07 13:32:49.440 UTC [804] LOG:  duration: 7.249 ms  plan:
	Query Text: SELECT fk."city" FROM ONLY "public"."stations" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)
	Hash Anti Join  (cost=573.00..1087.43 rows=1 width=4)
	  Hash Cond: (fk.city = pk.city_id)
	  ->  Seq Scan on stations fk  (cost=0.00..353.07 rows=19708 width=4)
	        Filter: (city IS NOT NULL)
	  ->  Hash  (cost=323.00..323.00 rows=20000 width=4)
	        ->  Seq Scan on cities pk  (cost=0.00..323.00 rows=20000 width=4)
2021-01-07 13:32:49.440 UTC [804] CONTEXT:  SQL statement "SELECT fk."city" FROM ONLY "public"."stations" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)"
2021-01-07 13:32:49.451 UTC [804] LOG:  duration: 8.725 ms  plan:
	Query Text: SELECT fk."producer" FROM ONLY "public"."trains" fk LEFT OUTER JOIN ONLY "public"."producers" pk ON ( pk."producer_id" OPERATOR(pg_catalog.=) fk."producer") WHERE pk."producer_id" IS NULL AND (fk."producer" IS NOT NULL)
	Hash Anti Join  (cost=754.00..1303.93 rows=1 width=4)
	  Hash Cond: (fk.producer = pk.producer_id)
	  ->  Seq Scan on trains fk  (cost=0.00..387.00 rows=19900 width=4)
	        Filter: (producer IS NOT NULL)
	  ->  Hash  (cost=504.00..504.00 rows=20000 width=4)
	        ->  Seq Scan on producers pk  (cost=0.00..504.00 rows=20000 width=4)
2021-01-07 13:32:49.451 UTC [804] CONTEXT:  SQL statement "SELECT fk."producer" FROM ONLY "public"."trains" fk LEFT OUTER JOIN ONLY "public"."producers" pk ON ( pk."producer_id" OPERATOR(pg_catalog.=) fk."producer") WHERE pk."producer_id" IS NULL AND (fk."producer" IS NOT NULL)"
2021-01-07 13:32:49.457 UTC [804] LOG:  duration: 3.851 ms  plan:
	Query Text: SELECT fk."train_type" FROM ONLY "public"."trains" fk LEFT OUTER JOIN ONLY "public"."train_types" pk ON ( pk."train_type_id" OPERATOR(pg_catalog.=) fk."train_type") WHERE pk."train_type_id" IS NULL AND (fk."train_type" IS NOT NULL)
	Hash Anti Join  (cost=3.25..553.18 rows=1 width=4)
	  Hash Cond: (fk.train_type = pk.train_type_id)
	  ->  Seq Scan on trains fk  (cost=0.00..387.00 rows=19900 width=4)
	        Filter: (train_type IS NOT NULL)
	  ->  Hash  (cost=2.00..2.00 rows=100 width=4)
	        ->  Seq Scan on train_types pk  (cost=0.00..2.00 rows=100 width=4)
2021-01-07 13:32:49.457 UTC [804] CONTEXT:  SQL statement "SELECT fk."train_type" FROM ONLY "public"."trains" fk LEFT OUTER JOIN ONLY "public"."train_types" pk ON ( pk."train_type_id" OPERATOR(pg_catalog.=) fk."train_type") WHERE pk."train_type_id" IS NULL AND (fk."train_type" IS NOT NULL)"
2021-01-07 13:32:49.474 UTC [804] LOG:  duration: 15.532 ms  plan:
	Query Text: SELECT fk."train" FROM ONLY "public"."wagons" fk LEFT OUTER JOIN ONLY "public"."trains" pk ON ( pk."train_id"::pg_catalog.text OPERATOR(pg_catalog.=) fk."train"::pg_catalog.text) WHERE pk."train_id" IS NULL AND (fk."train" IS NOT NULL)
	Hash Anti Join  (cost=637.00..1698.24 rows=1 width=32)
	  Hash Cond: ((fk.train)::text = (pk.train_id)::text)
	  ->  Seq Scan on wagons fk  (cost=0.00..734.76 rows=39876 width=32)
	        Filter: (train IS NOT NULL)
	  ->  Hash  (cost=387.00..387.00 rows=20000 width=32)
	        ->  Seq Scan on trains pk  (cost=0.00..387.00 rows=20000 width=32)
2021-01-07 13:32:49.474 UTC [804] CONTEXT:  SQL statement "SELECT fk."train" FROM ONLY "public"."wagons" fk LEFT OUTER JOIN ONLY "public"."trains" pk ON ( pk."train_id"::pg_catalog.text OPERATOR(pg_catalog.=) fk."train"::pg_catalog.text) WHERE pk."train_id" IS NULL AND (fk."train" IS NOT NULL)"
```

Select stations (more than 1000 times):

```sql
2021-01-07 13:32:49.541 UTC [807] LOG:  duration: 0.047 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 18513)
```