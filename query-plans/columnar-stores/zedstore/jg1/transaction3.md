# Create stations

```sql
2021-01-08 14:23:23.320 UTC [735] LOG:  duration: 0.111 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."cities" x WHERE "city_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using cities_pkey on cities x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (city_id = 9482)
2021-01-08 14:23:23.320 UTC [735] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."cities" x WHERE "city_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-08 14:23:23.320 UTC [735] LOG:  duration: 6.696 ms  plan:
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
	    ->  Seq Scan on cities  (cost=0.00..373.00 rows=1 width=4)
	          Filter: ((city_name)::text = 'Görlitz'::text)
	  ->  Result  (cost=0.00..0.02 rows=1 width=48)
2021-01-08 14:23:23.327 UTC [735] LOG:  duration: 0.037 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."cities" x WHERE "city_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using cities_pkey on cities x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (city_id = 10544)
2021-01-08 14:23:23.327 UTC [735] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."cities" x WHERE "city_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-08 14:23:23.327 UTC [735] LOG:  duration: 3.143 ms  plan:
	Query Text: INSERT INTO stations (station_name, nr_platforms, city, date_created) VALUES (
		'Berlin Görlitzer Bhf', 6, (
			SELECT city_id FROM cities WHERE city_name LIKE '%Berlin%'
		), '1940-03-14'
	);
	Insert on stations  (cost=373.00..373.01 rows=0 width=0)
	  InitPlan 1 (returns $0)
	    ->  Seq Scan on cities  (cost=0.00..373.00 rows=2 width=4)
	          Filter: ((city_name)::text ~~ '%Berlin%'::text)
	  ->  Result  (cost=0.00..0.01 rows=1 width=48)
2021-01-08 14:23:23.341 UTC [735] LOG:  duration: 0.025 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 20002)
2021-01-08 14:23:23.341 UTC [735] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-08 14:23:23.342 UTC [735] LOG:  duration: 0.025 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 20001)
2021-01-08 14:23:23.342 UTC [735] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-08 14:23:23.342 UTC [735] LOG:  duration: 0.012 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 20001)
2021-01-08 14:23:23.342 UTC [735] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-08 14:23:23.342 UTC [735] LOG:  duration: 0.011 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 20002)
2021-01-08 14:23:23.342 UTC [735] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
```

# Create routes

```sql
2021-01-08 14:23:23.342 UTC [735] LOG:  duration: 10.455 ms  plan:
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
	    ->  Seq Scan on stations  (cost=0.00..402.59 rows=1 width=4)
	          Filter: ((station_name)::text = 'Görlitz HBF'::text)
	  InitPlan 2 (returns $1)
	    ->  Seq Scan on stations stations_1  (cost=0.00..402.59 rows=1 width=4)
	          Filter: ((station_name)::text = 'Berlin Görlitzer Bhf'::text)
	  InitPlan 3 (returns $2)
	    ->  Seq Scan on stations stations_2  (cost=0.00..402.59 rows=1 width=4)
	          Filter: ((station_name)::text = 'Berlin Görlitzer Bhf'::text)
	  InitPlan 4 (returns $3)
	    ->  Seq Scan on stations stations_3  (cost=0.00..402.59 rows=1 width=4)
	          Filter: ((station_name)::text = 'Görlitz HBF'::text)
	  ->  Values Scan on "*VALUES*"  (cost=0.00..0.04 rows=2 width=20)
```

# Create connections

Generate ending times, for each starting time:

```sql
2021-01-08 14:23:23.364 UTC [735] LOG:  duration: 0.042 ms  plan:
	Query Text: SELECT $1 - (SELECT floor(random()*($1 / 4 - 0))+10)
	Result  (cost=0.02..0.03 rows=1 width=8)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.02 rows=1 width=8)
2021-01-08 14:23:23.364 UTC [735] CONTEXT:  SQL statement "SELECT $1 - (SELECT floor(random()*($1 / 4 - 0))+10)"
	PL/pgSQL function calc_avg_speed(numeric,numeric) line 4 at RETURN
	SQL statement "SELECT $1 + (
				SELECT calc_duration ((
					SELECT calc_avg_speed($2, $4)
				), $3))"
	PL/pgSQL function calc_end_time(timestamp without time zone,integer,integer,integer) line 3 at RETURN
2021-01-08 14:23:23.364 UTC [735] LOG:  duration: 0.002 ms  plan:
	Query Text: SELECT (SELECT result * interval '1 hour')
	Result  (cost=0.01..0.02 rows=1 width=16)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.01 rows=1 width=16)
2021-01-08 14:23:23.364 UTC [735] CONTEXT:  SQL statement "SELECT (SELECT result * interval '1 hour')"
	PL/pgSQL function calc_duration(numeric,numeric) line 5 at RETURN
	SQL statement "SELECT $1 + (
				SELECT calc_duration ((
					SELECT calc_avg_speed($2, $4)
				), $3))"
	PL/pgSQL function calc_end_time(timestamp without time zone,integer,integer,integer) line 3 at RETURN
2021-01-08 14:23:23.364 UTC [735] LOG:  duration: 1.008 ms  plan:
	Query Text: SELECT $1 + (
				SELECT calc_duration ((
					SELECT calc_avg_speed($2, $4)
				), $3))
	Result  (cost=0.52..0.53 rows=1 width=8)
	  InitPlan 2 (returns $1)
	    ->  Result  (cost=0.26..0.52 rows=1 width=16)
	          InitPlan 1 (returns $0)
	            ->  Result  (cost=0.00..0.26 rows=1 width=4)
2021-01-08 14:23:23.364 UTC [735] CONTEXT:  SQL statement "SELECT $1 + (
				SELECT calc_duration ((
					SELECT calc_avg_speed($2, $4)
				), $3))"
	PL/pgSQL function calc_end_time(timestamp without time zone,integer,integer,integer) line 3 at RETURN
```

Select train and route for every connection:

```sql
2021-01-08 14:23:24.788 UTC [735] LOG:  duration: 0.018 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."routes" x WHERE "route_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using routes_pkey on routes x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (route_id = 20001)
2021-01-08 14:23:24.788 UTC [735] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."routes" x WHERE "route_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-08 14:23:24.788 UTC [735] LOG:  duration: 0.042 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."trains" x WHERE "train_id"::pg_catalog.text OPERATOR(pg_catalog.=) $1::pg_catalog.text FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using trains_pkey on trains x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: ((train_id)::text = 'bywdfevctetzjdwvofemzctyerwshr'::text)
2021-01-08 14:23:24.788 UTC [735] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."trains" x WHERE "train_id"::pg_catalog.text OPERATOR(pg_catalog.=) $1::pg_catalog.text FOR KEY SHARE OF x"
```

Finally insert connections:

```sql
2021-01-08 14:23:25.874 UTC [735] LOG:  duration: 2525.808 ms  plan:
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
	Insert on schedule  (cost=2137.61..20085.14 rows=0 width=0)
	  CTE selected_routes
	    ->  Seq Scan on routes  (cost=1610.35..2126.61 rows=6 width=20)
	          Filter: ((start_station = $0) OR (start_station = $1) OR (end_station = $2) OR (end_station = $3))
	          InitPlan 1 (returns $0)
	            ->  Seq Scan on stations  (cost=0.00..402.59 rows=1 width=4)
	                  Filter: ((station_name)::text = 'Görlitz HBF'::text)
	          InitPlan 2 (returns $1)
	            ->  Seq Scan on stations stations_1  (cost=0.00..402.59 rows=1 width=4)
	                  Filter: ((station_name)::text = 'Berlin Görlitzer Bhf'::text)
	          InitPlan 3 (returns $2)
	            ->  Seq Scan on stations stations_2  (cost=0.00..402.59 rows=1 width=4)
	                  Filter: ((station_name)::text = 'Görlitz HBF'::text)
	          InitPlan 4 (returns $3)
	            ->  Seq Scan on stations stations_3  (cost=0.00..402.59 rows=1 width=4)
	                  Filter: ((station_name)::text = 'Berlin Görlitzer Bhf'::text)
	  CTE train
	    ->  Index Scan using trains_pkey on trains  (cost=0.29..8.30 rows=1 width=47)
	          Index Cond: ((train_id)::text = 'bywdfevctetzjdwvofemzctyerwshr'::text)
	  ->  Nested Loop  (cost=2.70..17950.22 rows=6000 width=56)
	        ->  Nested Loop  (cost=0.01..85.23 rows=6000 width=44)
	              ->  Function Scan on generate_series start_time  (cost=0.01..10.01 rows=1000 width=8)
	              ->  Materialize  (cost=0.00..0.23 rows=6 width=36)
	                    ->  Nested Loop  (cost=0.00..0.20 rows=6 width=36)
	                          ->  CTE Scan on train  (cost=0.00..0.02 rows=1 width=32)
	                          ->  CTE Scan on selected_routes sr1  (cost=0.00..0.12 rows=6 width=4)
	        ->  Result  (cost=2.69..2.95 rows=1 width=8)
	              InitPlan 7 (returns $6)
	                ->  Hash Join  (cost=0.03..2.42 rows=1 width=4)
	                      Hash Cond: (train_types.train_type_id = train_1.train_type)
	                      ->  Seq Scan on train_types  (cost=0.00..2.00 rows=100 width=8)
	                      ->  Hash  (cost=0.02..0.02 rows=1 width=4)
	                            ->  CTE Scan on train train_1  (cost=0.00..0.02 rows=1 width=4)
	              InitPlan 8 (returns $8)
	                ->  CTE Scan on selected_routes sr2  (cost=0.00..0.14 rows=1 width=4)
	                      Filter: ($7 = route_id)
	              InitPlan 9 (returns $9)
	                ->  CTE Scan on selected_routes sr3  (cost=0.00..0.14 rows=1 width=4)
	                      Filter: ($7 = route_id)
```