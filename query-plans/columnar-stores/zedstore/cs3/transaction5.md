# Calculate occupancy rates

More than 1000 times:

```sql
2021-01-08 14:08:54.932 UTC [523] LOG:  duration: 3.989 ms  plan:
	Query Text: SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0
	Result  (cost=860.73..860.75 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Aggregate  (cost=860.72..860.73 rows=1 width=8)
	          ->  Seq Scan on passengers_schedule  (cost=0.00..860.72 rows=1 width=0)
	                Filter: ((seat = 855340) AND (connection = 2784))
2021-01-08 14:08:54.932 UTC [523] CONTEXT:  SQL statement "SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0"
	PL/pgSQL function is_seat_occupied(integer,integer) line 3 at RETURN
	SQL statement "SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)"
	PL/pgSQL function get_seats_from_connection(integer) line 15 at assignment
	SQL statement "SELECT COUNT(seat)::real / (SELECT Count(*) FROM get_seats_from_connection($1))::real  FROM get_seats_from_connection($1)
					WHERE is_taken = TRUE"
	PL/pgSQL function calc_occupancy_rate(integer) line 3 at assignment
2021-01-08 14:08:54.932 UTC [523] LOG:  duration: 4.307 ms  plan:
	Query Text: SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)
	Result  (cost=0.26..0.27 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.26 rows=1 width=1)
2021-01-08 14:08:54.932 UTC [523] CONTEXT:  SQL statement "SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)"
	PL/pgSQL function get_seats_from_connection(integer) line 15 at assignment
	SQL statement "SELECT COUNT(seat)::real / (SELECT Count(*) FROM get_seats_from_connection($1))::real  FROM get_seats_from_connection($1)
					WHERE is_taken = TRUE"
	PL/pgSQL function calc_occupancy_rate(integer) line 3 at assignment
```

Finally:

```sql
2021-01-08 14:09:00.866 UTC [523] LOG:  duration: 91.342 ms  plan:
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
	                                Index Cond: (connection_id = $1)
	                          ->  Index Only Scan using trains_pkey on trains  (cost=0.29..8.30 rows=1 width=32)
	                                Index Cond: (train_id = (schedule.train)::text)
2021-01-08 14:09:00.866 UTC [523] CONTEXT:  PL/pgSQL function get_seats_from_connection(integer) line 5 at FOR over SELECT rows
	SQL statement "SELECT COUNT(seat)::real / (SELECT Count(*) FROM get_seats_from_connection($1))::real  FROM get_seats_from_connection($1)
					WHERE is_taken = TRUE"
	PL/pgSQL function calc_occupancy_rate(integer) line 3 at assignment
2021-01-08 14:09:00.866 UTC [523] LOG:  duration: 458.971 ms  plan:
	Query Text: SELECT COUNT(seat)::real / (SELECT Count(*) FROM get_seats_from_connection($1))::real  FROM get_seats_from_connection($1)
					WHERE is_taken = TRUE
	Aggregate  (cost=24.26..24.28 rows=1 width=4)
	  InitPlan 1 (returns $0)
	    ->  Aggregate  (cost=12.75..12.76 rows=1 width=8)
	          ->  Function Scan on get_seats_from_connection get_seats_from_connection_1  (cost=0.25..10.25 rows=1000 width=0)
	  ->  Function Scan on get_seats_from_connection  (cost=0.25..10.25 rows=500 width=4)
	        Filter: is_taken
2021-01-08 14:09:00.866 UTC [523] CONTEXT:  SQL statement "SELECT COUNT(seat)::real / (SELECT Count(*) FROM get_seats_from_connection($1))::real  FROM get_seats_from_connection($1)
					WHERE is_taken = TRUE"
	PL/pgSQL function calc_occupancy_rate(integer) line 3 at assignment
```

# Main Query

```sql
2021-01-08 14:09:00.867 UTC [523] LOG:  duration: 6046.884 ms  plan:
	Query Text: /*
	* Which trains arrive the fullest?
	* → Show all arriving trains for given station on a given day
	* with their occupancy rates
	* with highest occupancy first.
	*
	* SCRIPT PARAMS:
	* station: the end station for which this is shown, varchar
	* day: the date, date
	*
	* EXEC SCRIPT:
	* psql -v station="'STATION'" -v day="'2020-08-22'" railway-system < transactions/transaction5_cs3.sql
	*
	* TEST VALUES:
	psql -v station="'Weinsberg East'" [DB-NAME] < transactions/zedstore/transaction5_cs3.sql
	
	* NOTE: We see as result= 0.00% due to of the huge number of seat in a seat => no, it was because of rounding of the division
	*/
	WITH connections AS (
		SELECT * FROM schedule
		JOIN routes ON route = route_id
		WHERE end_station = (
			SELECT station_id FROM station_names_cs
			WHERE station_name = 'Weinsberg East'
		)
	)
	Select q1.nm as Origin, end_time::date AS arrival_date, end_time::time AS arrival_time, q2.rate from
	    (SELECT s.station_name as nm, sch.end_time, connections.connection_id as cnt
	        from stations as s, schedule as sch, routes as r, connections
	        where connections.connection_id =sch.connection_id and sch.route = r.route_id and r.start_station= s.station_id) as q1
	    Inner join
	        (SELECT cor.connection as cnt, format_to_percentage(cor.rate) as rate from connections ,calc_occupancy_rate(connections.connection_id)  as cor) as q2
	    ON q1.cnt=q2.cnt
	    ORDER BY q2.rate DESC;
	Sort  (cost=11881.48..12001.53 rows=48020 width=76)
	  Sort Key: ((to_char(('100'::double precision * cor.rate), '999D99%'::text))::character varying) DESC
	  CTE connections
	    ->  Hash Join  (cost=500.14..948.85 rows=98 width=76)
	          Hash Cond: (schedule.route = routes.route_id)
	          InitPlan 1 (returns $0)
	            ->  Seq Scan on station_names_cs  (cost=0.00..129.38 rows=32 width=4)
	                  Filter: ((station_name)::text = 'Weinsberg East'::text)
	          ->  Seq Scan on schedule  (cost=0.00..397.41 rows=19541 width=56)
	          ->  Hash  (cost=369.54..369.54 rows=98 width=20)
	                ->  Seq Scan on routes  (cost=0.00..369.54 rows=98 width=20)
	                      Filter: (end_station = $0)
	  ->  Hash Join  (cost=543.99..5063.75 rows=48020 width=76)
	        Hash Cond: (cor.connection = connections.connection_id)
	        ->  Nested Loop  (cost=0.25..2452.21 rows=98000 width=36)
	              ->  CTE Scan on connections connections_1  (cost=0.00..1.96 rows=98 width=4)
	              ->  Function Scan on calc_occupancy_rate cor  (cost=0.25..10.25 rows=1000 width=8)
	        ->  Hash  (cost=542.51..542.51 rows=98 width=48)
	              ->  Nested Loop  (cost=3.76..542.51 rows=98 width=48)
	                    ->  Nested Loop  (cost=3.47..508.37 rows=98 width=20)
	                          ->  Hash Join  (cost=3.19..474.85 rows=98 width=20)
	                                Hash Cond: (sch.connection_id = connections.connection_id)
	                                ->  Seq Scan on schedule sch  (cost=0.00..397.41 rows=19541 width=16)
	                                ->  Hash  (cost=1.96..1.96 rows=98 width=4)
	                                      ->  CTE Scan on connections  (cost=0.00..1.96 rows=98 width=4)
	                          ->  Index Scan using routes_pkey on routes r  (cost=0.29..0.34 rows=1 width=8)
	                                Index Cond: (route_id = sch.route)
	                    ->  Index Scan using stations_pkey on stations s  (cost=0.29..0.35 rows=1 width=36)
	                          Index Cond: (station_id = r.start_station)
```