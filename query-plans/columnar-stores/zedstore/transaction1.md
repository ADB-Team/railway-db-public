# Prerequisites

## Getting the connection

```sql
Query Text: 
			SELECT connection_id FROM schedule WHERE route = (
				SELECT route_id FROM routes
				WHERE start_station = (
					SELECT station_id
					FROM stations
					WHERE station_name = $1
				)
				AND end_station = (
					SELECT station_id
					FROM stations
					WHERE station_name = $2
				)
			)
			AND start_time = $3
			AND train = $4
		
	Seq Scan on schedule  (cost=1136.62..1680.59 rows=1 width=4)
	  Filter: ((route = $2) AND (start_time = $3) AND ((train)::text = ($4)::text))
	  InitPlan 3 (returns $2)
	    ->  Seq Scan on routes  (cost=805.17..1136.62 rows=1 width=4)
	          Filter: ((start_station = $0) AND (end_station = $1))
	          InitPlan 1 (returns $0)
	            ->  Seq Scan on stations  (cost=0.00..402.59 rows=1 width=4)
	                  Filter: ((station_name)::text = ($1)::text)
	          InitPlan 2 (returns $1)
	            ->  Seq Scan on stations stations_1  (cost=0.00..402.59 rows=1 width=4)
	                  Filter: ((station_name)::text = ($2)::text)
```

## Getting the seats

39 times:

```sql
2021-01-06 19:01:51.663 UTC [574] LOG:  duration: 3.614 ms  plan:
	Query Text: SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0
	Result  (cost=798.51..798.53 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Aggregate  (cost=798.50..798.51 rows=1 width=8)
	          ->  Seq Scan on passengers_schedule  (cost=0.00..798.50 rows=1 width=0)
	                Filter: ((seat = 371426) AND (connection = 1))
2021-01-06 19:01:51.663 UTC [574] CONTEXT:  SQL statement "SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0"
	PL/pgSQL function is_seat_occupied(integer,integer) line 3 at RETURN
	SQL statement "SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)"
	PL/pgSQL function get_seats_from_connection(integer) line 15 at assignment
2021-01-06 19:01:51.663 UTC [574] LOG:  duration: 4.007 ms  plan:
	Query Text: SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)
	Result  (cost=0.26..0.27 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.26 rows=1 width=1)
2021-01-06 19:01:51.663 UTC [574] CONTEXT:  SQL statement "SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)"
	PL/pgSQL function get_seats_from_connection(integer) line 15 at assignment
```

Finally:

```sql
2021-01-06 19:01:51.811 UTC [574] CONTEXT:  SQL statement "SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0"
	PL/pgSQL function is_seat_occupied(integer,integer) line 3 at RETURN
	SQL statement "SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)"
	PL/pgSQL function get_seats_from_connection(integer) line 15 at assignment
2021-01-06 19:01:51.811 UTC [574] LOG:  duration: 2.676 ms  plan:
	Query Text: SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)
	Result  (cost=0.26..0.27 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.26 rows=1 width=1)
2021-01-06 19:01:51.811 UTC [574] CONTEXT:  SQL statement "SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)"
	PL/pgSQL function get_seats_from_connection(integer) line 15 at assignment
```

Function `get_seats_from_connection`:

```sql
2021-01-06 19:01:51.812 UTC [574] LOG:  duration: 87.974 ms  plan:
	Query Text: (
					SELECT seat_id
					FROM seats
					JOIN wagons ON wagon = wagon_id
					JOIN trains ON wagons.train = train_id
					JOIN schedule ON schedule.train = train_id
					WHERE connection_id = $1
				)
	Hash Join  (cost=848.96..18098.90 rows=45 width=4)
	  Hash Cond: (seats.wagon = wagons.wagon_id)
	  ->  Seq Scan on seats  (cost=0.00..13872.72 rows=900472 width=8)
	  ->  Hash  (cost=848.93..848.93 rows=2 width=4)
	        ->  Nested Loop  (cost=8.60..848.93 rows=2 width=4)
	              ->  Hash Join  (cost=8.32..848.30 rows=2 width=66)
	                    Hash Cond: ((wagons.train)::text = (schedule.train)::text)
	                    ->  Seq Scan on wagons  (cost=0.00..734.76 rows=40076 width=35)
	                    ->  Hash  (cost=8.30..8.30 rows=1 width=31)
	                          ->  Index Scan using schedule_pkey on schedule  (cost=0.29..8.30 rows=1 width=31)
	                                Index Cond: (connection_id = 1)
	              ->  Index Only Scan using trains_pkey on trains  (cost=0.29..0.32 rows=1 width=31)
	                    Index Cond: (train_id = (wagons.train)::text)
2021-01-06 19:01:51.812 UTC [574] CONTEXT:  PL/pgSQL function get_seats_from_connection(integer) line 5 at FOR over SELECT rows
```

For every passenger:

```sql
2021-01-06 19:01:51.817 UTC [574] LOG:  duration: 0.030 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."schedule" x WHERE "connection_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using schedule_pkey on schedule x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (connection_id = 1)
2021-01-06 19:01:51.817 UTC [574] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."schedule" x WHERE "connection_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-06 19:01:51.818 UTC [574] LOG:  duration: 0.040 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."passengers" x WHERE "passenger_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using passengers_pkey on passengers x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (passenger_id = 1)
2021-01-06 19:01:51.818 UTC [574] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."passengers" x WHERE "passenger_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-06 19:01:51.818 UTC [574] LOG:  duration: 0.063 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."seats" x WHERE "seat_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.42..8.45 rows=1 width=10)
	  ->  Index Scan using seats_pkey on seats x  (cost=0.42..8.44 rows=1 width=10)
	        Index Cond: (seat_id = 371426)
2021-01-06 19:01:51.818 UTC [574] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."seats" x WHERE "seat_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
```

# Main Query

```sql
Query Text: /*
	* A family buys a ticket.
	* → A new row for every person with a given surname is inserted in passengers_schedule.
	*
	* SCRIPT PARAMS:
	* surname: family name, varchar
	* start_station: name of start station of connection, varchar
	* end_station: name of destination station of connection, varchar
	* train_id: code of train, varchar
	* start_time: start time and date of connection, timestamp
	*
	* EXEC SCRIPT:
	* psql -v surname="'SURNAME'" -v name="'NAME'" -v phone_number="'PHONE_NR'" -v start_station="'START'" -v end_station="'END'" -v train_id="'TRAIN'" -v start_time="'DATE & TIME'" [DB-NAME] < transactions/transaction1.sql
	*
	* TEST VALUES:
	psql -v surname="'Cooper'" -v start_station="'Carnegie Central'" -v end_station="'Bytča West'" -v train_id="'cglytjrbckkgdnfegmsjpirvqpkpsh'" -v start_time="'2003-08-13 05:01:10.879157'" [DB-NAME] < transactions/transaction1.sql
	*/
	WITH con AS (
		SELECT connection_id FROM get_connection(
			'Carnegie Central'::varchar,
			'Bytča West'::varchar,
			'2003-08-13 05:01:10.879157'::timestamp,
			'cglytjrbckkgdnfegmsjpirvqpkpsh'::varchar
		)
	),
	free_seats AS (
		SELECT seat FROM (
			SELECT seat FROM get_seats_from_connection((SELECT * FROM con))
			AS train_seats
			WHERE is_taken = FALSE
		) AS seats
	),
	family AS (
		SELECT passenger_id, connection_id, seat FROM passengers, con, free_seats
		WHERE surname = 'Cooper'
	)
	INSERT INTO passengers_schedule (passenger, connection, seat)
	(SELECT passenger_id, connection_id, seat FROM family)
	RETURNING passenger, connection, seat;
	Insert on passengers_schedule  (cost=40.50..307259.12 rows=24500000 width=12)
	  CTE con
	    ->  Function Scan on get_connection  (cost=0.25..10.25 rows=1000 width=4)
	  CTE free_seats
	    ->  Function Scan on get_seats_from_connection train_seats  (cost=20.25..30.25 rows=500 width=4)
	          Filter: (NOT is_taken)
	          InitPlan 2 (returns $1)
	            ->  CTE Scan on con con_1  (cost=0.00..20.00 rows=1000 width=4)
	  ->  Nested Loop  (cost=0.00..307218.62 rows=24500000 width=12)
	        ->  CTE Scan on con  (cost=0.00..20.00 rows=1000 width=4)
	        ->  Materialize  (cost=0.00..1009.87 rows=24500 width=8)
	              ->  Nested Loop  (cost=0.00..887.37 rows=24500 width=8)
	                    ->  CTE Scan on free_seats  (cost=0.00..10.00 rows=500 width=4)
	                    ->  Materialize  (cost=0.00..571.25 rows=49 width=4)
	                          ->  Seq Scan on passengers  (cost=0.00..571.00 rows=49 width=4)
	                                Filter: ((surname)::text = 'Cooper'::text)
```

Configuration of catalog values:

```sql
2021-01-06 19:08:26.595 UTC [615] LOG:  duration: 0.035 ms  plan:
	Query Text: SELECT pg_catalog.set_config('search_path', '', false);
	Result  (cost=0.00..0.01 rows=1 width=32)
2021-01-06 19:08:26.662 UTC [617] LOG:  duration: 0.013 ms  plan:
	Query Text: SELECT pg_catalog.set_config('search_path', '', false);
	Result  (cost=0.00..0.01 rows=1 width=32)
2021-01-06 19:08:26.783 UTC [619] LOG:  duration: 0.006 ms  plan:
	Query Text: SELECT pg_catalog.set_config('search_path', '', false);
	Result  (cost=0.00..0.01 rows=1 width=32)
2021-01-06 19:08:28.067 UTC [619] LOG:  duration: 0.019 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.cities_city_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.068 UTC [619] LOG:  duration: 0.031 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.countries_country_id_seq', 223, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.070 UTC [619] LOG:  duration: 0.035 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.passengers_passenger_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.071 UTC [619] LOG:  duration: 0.036 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.producers_producer_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.072 UTC [619] LOG:  duration: 0.036 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.routes_route_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.074 UTC [619] LOG:  duration: 0.062 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.schedule_connection_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.075 UTC [619] LOG:  duration: 0.026 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.seats_seat_id_seq', 900472, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.076 UTC [619] LOG:  duration: 0.020 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.stations_station_id_seq', 20000, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.076 UTC [619] LOG:  duration: 0.017 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.train_types_train_type_id_seq', 100, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
2021-01-06 19:08:28.077 UTC [619] LOG:  duration: 0.015 ms  plan:
	Query Text: SELECT pg_catalog.setval('public.wagons_wagon_id_seq', 40076, true);
	Result  (cost=0.00..0.01 rows=1 width=8)
```

Joining tables together:

```sql
2021-01-06 19:08:28.745 UTC [619] CONTEXT:  SQL statement "SELECT fk."country" FROM ONLY "public"."cities" fk LEFT OUTER JOIN ONLY "public"."countries" pk ON ( pk."country_id" OPERATOR(pg_catalog.=) fk."country") WHERE pk."country_id" IS NULL AND (fk."country" IS NOT NULL)"
2021-01-06 19:08:28.758 UTC [619] LOG:  duration: 11.752 ms  plan:
	Query Text: SELECT fk."city" FROM ONLY "public"."passengers" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)
	Hash Anti Join  (cost=573.00..1256.93 rows=1 width=4)
	  Hash Cond: (fk.city = pk.city_id)
	  ->  Seq Scan on passengers fk  (cost=0.00..521.00 rows=19900 width=4)
	        Filter: (city IS NOT NULL)
	  ->  Hash  (cost=323.00..323.00 rows=20000 width=4)
	        ->  Seq Scan on cities pk  (cost=0.00..323.00 rows=20000 width=4)
2021-01-06 19:08:28.758 UTC [619] CONTEXT:  SQL statement "SELECT fk."city" FROM ONLY "public"."passengers" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)"
2021-01-06 19:08:28.773 UTC [619] LOG:  duration: 13.195 ms  plan:
	Query Text: SELECT fk."connection" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."schedule" pk ON ( pk."connection_id" OPERATOR(pg_catalog.=) fk."connection") WHERE pk."connection_id" IS NULL AND (fk."connection" IS NOT NULL)
	Hash Anti Join  (cost=641.67..1638.48 rows=1 width=4)
	  Hash Cond: (fk.connection = pk.connection_id)
	  ->  Seq Scan on passengers_schedule fk  (cost=0.00..644.48 rows=43032 width=4)
	        Filter: (connection IS NOT NULL)
	  ->  Hash  (cost=397.41..397.41 rows=19541 width=4)
	        ->  Seq Scan on schedule pk  (cost=0.00..397.41 rows=19541 width=4)
2021-01-06 19:08:28.773 UTC [619] CONTEXT:  SQL statement "SELECT fk."connection" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."schedule" pk ON ( pk."connection_id" OPERATOR(pg_catalog.=) fk."connection") WHERE pk."connection_id" IS NULL AND (fk."connection" IS NOT NULL)"
2021-01-06 19:08:28.790 UTC [619] LOG:  duration: 14.796 ms  plan:
	Query Text: SELECT fk."passenger" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."passengers" pk ON ( pk."passenger_id" OPERATOR(pg_catalog.=) fk."passenger") WHERE pk."passenger_id" IS NULL AND (fk."passenger" IS NOT NULL)
	Hash Anti Join  (cost=771.00..1767.80 rows=1 width=4)
	  Hash Cond: (fk.passenger = pk.passenger_id)
	  ->  Seq Scan on passengers_schedule fk  (cost=0.00..644.48 rows=43032 width=4)
	        Filter: (passenger IS NOT NULL)
	  ->  Hash  (cost=521.00..521.00 rows=20000 width=4)
	        ->  Seq Scan on passengers pk  (cost=0.00..521.00 rows=20000 width=4)
2021-01-06 19:08:28.790 UTC [619] CONTEXT:  SQL statement "SELECT fk."passenger" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."passengers" pk ON ( pk."passenger_id" OPERATOR(pg_catalog.=) fk."passenger") WHERE pk."passenger_id" IS NULL AND (fk."passenger" IS NOT NULL)"
2021-01-06 19:08:29.015 UTC [619] LOG:  duration: 223.541 ms  plan:
	Query Text: SELECT fk."seat" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."seats" pk ON ( pk."seat_id" OPERATOR(pg_catalog.=) fk."seat") WHERE pk."seat_id" IS NULL AND (fk."seat" IS NOT NULL)
	Hash Anti Join  (cost=25128.62..26125.42 rows=1 width=4)
	  Hash Cond: (fk.seat = pk.seat_id)
	  ->  Seq Scan on passengers_schedule fk  (cost=0.00..644.48 rows=43032 width=4)
	        Filter: (seat IS NOT NULL)
	  ->  Hash  (cost=13872.72..13872.72 rows=900472 width=4)
	        ->  Seq Scan on seats pk  (cost=0.00..13872.72 rows=900472 width=4)
2021-01-06 19:08:29.015 UTC [619] CONTEXT:  SQL statement "SELECT fk."seat" FROM ONLY "public"."passengers_schedule" fk LEFT OUTER JOIN ONLY "public"."seats" pk ON ( pk."seat_id" OPERATOR(pg_catalog.=) fk."seat") WHERE pk."seat_id" IS NULL AND (fk."seat" IS NOT NULL)"
2021-01-06 19:08:29.027 UTC [619] LOG:  duration: 6.995 ms  plan:
	Query Text: SELECT fk."city" FROM ONLY "public"."producers" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)
	Hash Anti Join  (cost=573.00..1239.93 rows=1 width=4)
	  Hash Cond: (fk.city = pk.city_id)
	  ->  Seq Scan on producers fk  (cost=0.00..504.00 rows=19900 width=4)
	        Filter: (city IS NOT NULL)
	  ->  Hash  (cost=323.00..323.00 rows=20000 width=4)
	        ->  Seq Scan on cities pk  (cost=0.00..323.00 rows=20000 width=4)
2021-01-06 19:08:29.027 UTC [619] CONTEXT:  SQL statement "SELECT fk."city" FROM ONLY "public"."producers" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)"
2021-01-06 19:08:29.040 UTC [619] LOG:  duration: 10.908 ms  plan:
	Query Text: SELECT fk."end_station" FROM ONLY "public"."routes" fk LEFT OUTER JOIN ONLY "public"."stations" pk ON ( pk."station_id" OPERATOR(pg_catalog.=) fk."end_station") WHERE pk."station_id" IS NULL AND (fk."end_station" IS NOT NULL)
	Hash Anti Join  (cost=600.66..1080.66 rows=1 width=4)
	  Hash Cond: (fk.end_station = pk.station_id)
	  ->  Seq Scan on routes fk  (cost=0.00..320.63 rows=19465 width=4)
	        Filter: (end_station IS NOT NULL)
	  ->  Hash  (cost=353.07..353.07 rows=19807 width=4)
	        ->  Seq Scan on stations pk  (cost=0.00..353.07 rows=19807 width=4)
2021-01-06 19:08:29.040 UTC [619] CONTEXT:  SQL statement "SELECT fk."end_station" FROM ONLY "public"."routes" fk LEFT OUTER JOIN ONLY "public"."stations" pk ON ( pk."station_id" OPERATOR(pg_catalog.=) fk."end_station") WHERE pk."station_id" IS NULL AND (fk."end_station" IS NOT NULL)"
2021-01-06 19:08:29.055 UTC [619] LOG:  duration: 12.994 ms  plan:
	Query Text: SELECT fk."start_station" FROM ONLY "public"."routes" fk LEFT OUTER JOIN ONLY "public"."stations" pk ON ( pk."station_id" OPERATOR(pg_catalog.=) fk."start_station") WHERE pk."station_id" IS NULL AND (fk."start_station" IS NOT NULL)
	Hash Anti Join  (cost=600.66..1080.66 rows=1 width=4)
	  Hash Cond: (fk.start_station = pk.station_id)
	  ->  Seq Scan on routes fk  (cost=0.00..320.63 rows=19465 width=4)
	        Filter: (start_station IS NOT NULL)
	  ->  Hash  (cost=353.07..353.07 rows=19807 width=4)
	        ->  Seq Scan on stations pk  (cost=0.00..353.07 rows=19807 width=4)
2021-01-06 19:08:29.055 UTC [619] CONTEXT:  SQL statement "SELECT fk."start_station" FROM ONLY "public"."routes" fk LEFT OUTER JOIN ONLY "public"."stations" pk ON ( pk."station_id" OPERATOR(pg_catalog.=) fk."start_station") WHERE pk."station_id" IS NULL AND (fk."start_station" IS NOT NULL)"
2021-01-06 19:08:29.065 UTC [619] LOG:  duration: 7.594 ms  plan:
	Query Text: SELECT fk."route" FROM ONLY "public"."schedule" fk LEFT OUTER JOIN ONLY "public"."routes" pk ON ( pk."route_id" OPERATOR(pg_catalog.=) fk."route") WHERE pk."route_id" IS NULL AND (fk."route" IS NOT NULL)
	Hash Anti Join  (cost=565.17..1121.76 rows=1 width=4)
	  Hash Cond: (fk.route = pk.route_id)
	  ->  Seq Scan on schedule fk  (cost=0.00..397.41 rows=19443 width=4)
	        Filter: (route IS NOT NULL)
	  ->  Hash  (cost=320.63..320.63 rows=19563 width=4)
	        ->  Seq Scan on routes pk  (cost=0.00..320.63 rows=19563 width=4)
2021-01-06 19:08:29.065 UTC [619] CONTEXT:  SQL statement "SELECT fk."route" FROM ONLY "public"."schedule" fk LEFT OUTER JOIN ONLY "public"."routes" pk ON ( pk."route_id" OPERATOR(pg_catalog.=) fk."route") WHERE pk."route_id" IS NULL AND (fk."route" IS NOT NULL)"
2021-01-06 19:08:29.076 UTC [619] LOG:  duration: 8.618 ms  plan:
	Query Text: SELECT fk."train" FROM ONLY "public"."schedule" fk LEFT OUTER JOIN ONLY "public"."trains" pk ON ( pk."train_id"::pg_catalog.text OPERATOR(pg_catalog.=) fk."train"::pg_catalog.text) WHERE pk."train_id" IS NULL AND (fk."train" IS NOT NULL)
	Hash Anti Join  (cost=637.00..1193.60 rows=1 width=32)
	  Hash Cond: ((fk.train)::text = (pk.train_id)::text)
	  ->  Seq Scan on schedule fk  (cost=0.00..397.41 rows=19443 width=32)
	        Filter: (train IS NOT NULL)
	  ->  Hash  (cost=387.00..387.00 rows=20000 width=32)
	        ->  Seq Scan on trains pk  (cost=0.00..387.00 rows=20000 width=32)
2021-01-06 19:08:29.076 UTC [619] CONTEXT:  SQL statement "SELECT fk."train" FROM ONLY "public"."schedule" fk LEFT OUTER JOIN ONLY "public"."trains" pk ON ( pk."train_id"::pg_catalog.text OPERATOR(pg_catalog.=) fk."train"::pg_catalog.text) WHERE pk."train_id" IS NULL AND (fk."train" IS NOT NULL)"
2021-01-06 19:08:29.266 UTC [619] LOG:  duration: 188.122 ms  plan:
	Query Text: SELECT fk."wagon" FROM ONLY "public"."seats" fk LEFT OUTER JOIN ONLY "public"."wagons" pk ON ( pk."wagon_id" OPERATOR(pg_catalog.=) fk."wagon") WHERE pk."wagon_id" IS NULL AND (fk."wagon" IS NOT NULL)
	Hash Anti Join  (cost=1235.71..22444.18 rows=1 width=4)
	  Hash Cond: (fk.wagon = pk.wagon_id)
	  ->  Seq Scan on seats fk  (cost=0.00..13872.72 rows=895970 width=4)
	        Filter: (wagon IS NOT NULL)
	  ->  Hash  (cost=734.76..734.76 rows=40076 width=4)
	        ->  Seq Scan on wagons pk  (cost=0.00..734.76 rows=40076 width=4)
2021-01-06 19:08:29.266 UTC [619] CONTEXT:  SQL statement "SELECT fk."wagon" FROM ONLY "public"."seats" fk LEFT OUTER JOIN ONLY "public"."wagons" pk ON ( pk."wagon_id" OPERATOR(pg_catalog.=) fk."wagon") WHERE pk."wagon_id" IS NULL AND (fk."wagon" IS NOT NULL)"
2021-01-06 19:08:29.276 UTC [619] LOG:  duration: 6.873 ms  plan:
	Query Text: SELECT fk."city" FROM ONLY "public"."stations" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)
	Hash Anti Join  (cost=573.00..1087.43 rows=1 width=4)
	  Hash Cond: (fk.city = pk.city_id)
	  ->  Seq Scan on stations fk  (cost=0.00..353.07 rows=19708 width=4)
	        Filter: (city IS NOT NULL)
	  ->  Hash  (cost=323.00..323.00 rows=20000 width=4)
	        ->  Seq Scan on cities pk  (cost=0.00..323.00 rows=20000 width=4)
2021-01-06 19:08:29.276 UTC [619] CONTEXT:  SQL statement "SELECT fk."city" FROM ONLY "public"."stations" fk LEFT OUTER JOIN ONLY "public"."cities" pk ON ( pk."city_id" OPERATOR(pg_catalog.=) fk."city") WHERE pk."city_id" IS NULL AND (fk."city" IS NOT NULL)"
2021-01-06 19:08:29.288 UTC [619] LOG:  duration: 9.951 ms  plan:
	Query Text: SELECT fk."producer" FROM ONLY "public"."trains" fk LEFT OUTER JOIN ONLY "public"."producers" pk ON ( pk."producer_id" OPERATOR(pg_catalog.=) fk."producer") WHERE pk."producer_id" IS NULL AND (fk."producer" IS NOT NULL)
	Hash Anti Join  (cost=754.00..1303.93 rows=1 width=4)
	  Hash Cond: (fk.producer = pk.producer_id)
	  ->  Seq Scan on trains fk  (cost=0.00..387.00 rows=19900 width=4)
	        Filter: (producer IS NOT NULL)
	  ->  Hash  (cost=504.00..504.00 rows=20000 width=4)
	        ->  Seq Scan on producers pk  (cost=0.00..504.00 rows=20000 width=4)
2021-01-06 19:08:29.288 UTC [619] CONTEXT:  SQL statement "SELECT fk."producer" FROM ONLY "public"."trains" fk LEFT OUTER JOIN ONLY "public"."producers" pk ON ( pk."producer_id" OPERATOR(pg_catalog.=) fk."producer") WHERE pk."producer_id" IS NULL AND (fk."producer" IS NOT NULL)"
2021-01-06 19:08:29.294 UTC [619] LOG:  duration: 3.798 ms  plan:
	Query Text: SELECT fk."train_type" FROM ONLY "public"."trains" fk LEFT OUTER JOIN ONLY "public"."train_types" pk ON ( pk."train_type_id" OPERATOR(pg_catalog.=) fk."train_type") WHERE pk."train_type_id" IS NULL AND (fk."train_type" IS NOT NULL)
	Hash Anti Join  (cost=3.25..553.18 rows=1 width=4)
	  Hash Cond: (fk.train_type = pk.train_type_id)
	  ->  Seq Scan on trains fk  (cost=0.00..387.00 rows=19900 width=4)
	        Filter: (train_type IS NOT NULL)
	  ->  Hash  (cost=2.00..2.00 rows=100 width=4)
	        ->  Seq Scan on train_types pk  (cost=0.00..2.00 rows=100 width=4)
2021-01-06 19:08:29.294 UTC [619] CONTEXT:  SQL statement "SELECT fk."train_type" FROM ONLY "public"."trains" fk LEFT OUTER JOIN ONLY "public"."train_types" pk ON ( pk."train_type_id" OPERATOR(pg_catalog.=) fk."train_type") WHERE pk."train_type_id" IS NULL AND (fk."train_type" IS NOT NULL)"
2021-01-06 19:08:29.314 UTC [619] LOG:  duration: 18.222 ms  plan:
	Query Text: SELECT fk."train" FROM ONLY "public"."wagons" fk LEFT OUTER JOIN ONLY "public"."trains" pk ON ( pk."train_id"::pg_catalog.text OPERATOR(pg_catalog.=) fk."train"::pg_catalog.text) WHERE pk."train_id" IS NULL AND (fk."train" IS NOT NULL)
	Hash Anti Join  (cost=637.00..1698.24 rows=1 width=32)
	  Hash Cond: ((fk.train)::text = (pk.train_id)::text)
	  ->  Seq Scan on wagons fk  (cost=0.00..734.76 rows=39876 width=32)
	        Filter: (train IS NOT NULL)
	  ->  Hash  (cost=387.00..387.00 rows=20000 width=32)
	        ->  Seq Scan on trains pk  (cost=0.00..387.00 rows=20000 width=32)
2021-01-06 19:08:29.314 UTC [619] CONTEXT:  SQL statement "SELECT fk."train" FROM ONLY "public"."wagons" fk LEFT OUTER JOIN ONLY "public"."trains" pk ON ( pk."train_id"::pg_catalog.text OPERATOR(pg_catalog.=) fk."train"::pg_catalog.text) WHERE pk."train_id" IS NULL AND (fk."train" IS NOT NULL)"
```

Getting stations, for every passenger:

```sql
2021-01-06 19:08:36.446 UTC [622] LOG:  duration: 0.034 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."stations" x WHERE "station_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using stations_pkey on stations x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (station_id = 18513)
```

