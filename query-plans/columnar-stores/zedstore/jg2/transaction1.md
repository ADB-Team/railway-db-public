# Prerequisites

## Getting the connection

```sql
2021-01-08 14:39:44.686 UTC [1028] LOG:  duration: 12.721 ms  plan:
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
		
	Seq Scan on schedule  (cost=1223.62..1767.59 rows=1 width=4)
	  Filter: ((route = $2) AND (start_time = $3) AND ((train)::text = ($4)::text))
	  InitPlan 3 (returns $2)
	    ->  Seq Scan on routes  (cost=805.17..1223.62 rows=1 width=4)
	          Filter: ((start_station = $0) AND (end_station = $1))
	          InitPlan 1 (returns $0)
	            ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=4)
	                  Filter: ((station_name)::text = ($1)::text)
	          InitPlan 2 (returns $1)
	            ->  Seq Scan on stations stations_1  (cost=0.00..402.59 rows=99 width=4)
	                  Filter: ((station_name)::text = ($2)::text)
2021-01-08 14:39:44.686 UTC [1028] CONTEXT:  SQL function "get_connection" statement 1
```

## Getting seats

For every passenger:

```sql
2021-01-08 14:39:44.734 UTC [1028] LOG:  duration: 3.820 ms  plan:
	Query Text: SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0
	Result  (cost=860.73..860.75 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Aggregate  (cost=860.72..860.73 rows=1 width=8)
	          ->  Seq Scan on passengers_schedule  (cost=0.00..860.72 rows=1 width=0)
	                Filter: ((seat = 371426) AND (connection = 1))
2021-01-08 14:39:44.734 UTC [1028] CONTEXT:  SQL statement "SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0"
	PL/pgSQL function is_seat_occupied(integer,integer) line 3 at RETURN
	SQL statement "SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)"
	PL/pgSQL function get_seats_from_connection(integer) line 15 at assignment
2021-01-08 14:39:44.735 UTC [1028] LOG:  duration: 4.225 ms  plan:
	Query Text: SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)
	Result  (cost=0.26..0.27 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.26 rows=1 width=1)
2021-01-08 14:39:44.735 UTC [1028] CONTEXT:  SQL statement "SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)"
	PL/pgSQL function get_seats_from_connection(integer) line 15 at assignment
```

Finally:

```sql
2021-01-08 14:39:44.898 UTC [1028] LOG:  duration: 94.592 ms  plan:
	Query Text: (
					SELECT seat_id
					FROM seats
					JOIN wagons ON wagon = wagon_id
					JOIN trains ON wagons.train = train_id
					JOIN schedule ON schedule.train = train_id
					WHERE connection_id = $1
				)
	Hash Join  (cost=901.71..18151.65 rows=45 width=4)
	  Hash Cond: (seats.wagon = wagons.wagon_id)
	  ->  Seq Scan on seats  (cost=0.00..13872.72 rows=900472 width=8)
	  ->  Hash  (cost=901.69..901.69 rows=2 width=4)
	        ->  Hash Join  (cost=16.62..901.69 rows=2 width=4)
	              Hash Cond: ((wagons.train)::text = (trains.train_id)::text)
	              ->  Seq Scan on wagons  (cost=0.00..734.76 rows=40076 width=36)
	              ->  Hash  (cost=16.61..16.61 rows=1 width=64)
	                    ->  Nested Loop  (cost=0.57..16.61 rows=1 width=64)
	                          ->  Index Scan using schedule_pkey on schedule  (cost=0.29..8.30 rows=1 width=32)
	                                Index Cond: (connection_id = 1)
	                          ->  Index Only Scan using trains_pkey on trains  (cost=0.29..8.30 rows=1 width=32)
	                                Index Cond: (train_id = (schedule.train)::text)
2021-01-08 14:39:44.898 UTC [1028] CONTEXT:  PL/pgSQL function get_seats_from_connection(integer) line 5 at FOR over SELECT rows
```

## Selecting schedule, passenger, seat

Over 1000 times:

```sql
2021-01-08 14:39:44.904 UTC [1028] LOG:  duration: 0.017 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."schedule" x WHERE "connection_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using schedule_pkey on schedule x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (connection_id = 1)
2021-01-08 14:39:44.904 UTC [1028] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."schedule" x WHERE "connection_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-08 14:39:44.904 UTC [1028] LOG:  duration: 0.027 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."passengers" x WHERE "passenger_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10)
	  ->  Index Scan using passengers_pkey on passengers x  (cost=0.29..8.30 rows=1 width=10)
	        Index Cond: (passenger_id = 1)
2021-01-08 14:39:44.904 UTC [1028] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."passengers" x WHERE "passenger_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
2021-01-08 14:39:44.905 UTC [1028] LOG:  duration: 0.040 ms  plan:
	Query Text: SELECT 1 FROM ONLY "public"."seats" x WHERE "seat_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.42..8.45 rows=1 width=10)
	  ->  Index Scan using seats_pkey on seats x  (cost=0.42..8.44 rows=1 width=10)
	        Index Cond: (seat_id = 371426)
2021-01-08 14:39:44.905 UTC [1028] CONTEXT:  SQL statement "SELECT 1 FROM ONLY "public"."seats" x WHERE "seat_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x"
```

# Main Query

```sql
2021-01-08 14:39:45.049 UTC [1028] LOG:  duration: 377.281 ms  plan:
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
	Insert on passengers_schedule  (cost=40.50..626391.75 rows=50000000 width=12)
	  CTE con
	    ->  Function Scan on get_connection  (cost=0.25..10.25 rows=1000 width=4)
	  CTE free_seats
	    ->  Function Scan on get_seats_from_connection train_seats  (cost=20.25..30.25 rows=500 width=4)
	          Filter: (NOT is_taken)
	          InitPlan 2 (returns $1)
	            ->  CTE Scan on con con_1  (cost=0.00..20.00 rows=1000 width=4)
	  ->  Nested Loop  (cost=0.00..626351.25 rows=50000000 width=12)
	        ->  CTE Scan on con  (cost=0.00..20.00 rows=1000 width=4)
	        ->  Materialize  (cost=0.00..1456.25 rows=50000 width=8)
	              ->  Nested Loop  (cost=0.00..1206.25 rows=50000 width=8)
	                    ->  CTE Scan on free_seats  (cost=0.00..10.00 rows=500 width=4)
	                    ->  Materialize  (cost=0.00..571.50 rows=100 width=4)
	                          ->  Seq Scan on passengers  (cost=0.00..571.00 rows=100 width=4)
	                                Filter: ((surname)::text = 'Cooper'::text)
```

