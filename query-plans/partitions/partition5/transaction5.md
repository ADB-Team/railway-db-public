# Prerequisites

## Calculate occupancy rate

```sql
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
	        Buckets: 1024  Batches: 1  Memory Usage: 9kB
	        ->  Hash Join  (cost=16.62..901.69 rows=2 width=4)
	              Hash Cond: ((wagons.train)::text = (trains.train_id)::text)
	              ->  Seq Scan on wagons  (cost=0.00..734.76 rows=40076 width=36)
	              ->  Hash  (cost=16.61..16.61 rows=1 width=64)
	                    Buckets: 1024  Batches: 1  Memory Usage: 9kB
	                    ->  Nested Loop  (cost=0.57..16.61 rows=1 width=64)
	                          ->  Index Scan using schedule_pkey on schedule  (cost=0.29..8.30 rows=1 width=32)
	                                Index Cond: (connection_id = $1)
	                          ->  Index Only Scan using trains_pkey on trains  (cost=0.29..8.30 rows=1 width=32)
	                                Index Cond: (train_id = (schedule.train)::text)
2020-12-21 16:41:36.168 CET [36800] CONTEXTO:  función PL/pgSQL get_seats_from_connection(integer) en la línea 5 en bucle FOR en torno a filas de un SELECT
	sentencia SQL: «SELECT COUNT(seat)::real / (SELECT Count(*) FROM get_seats_from_connection($1))::real  FROM get_seats_from_connection($1)
					WHERE is_taken = TRUE»
```	

### For every connection:

```sql
	Query Text: SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0
	Result  (cost=430.37..430.38 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Aggregate  (cost=430.36..430.37 rows=1 width=8)
	          ->  Seq Scan on passengers_schedule  (cost=0.00..430.36 rows=1 width=0)
	                Filter: ((seat = $1) AND (connection = $2))
2020-12-21 16:41:36.181 CET [36800] CONTEXTO:  sentencia SQL: «SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0»
	función PL/pgSQL is_seat_occupied(integer,integer) en la línea 3 en RETURN
	sentencia SQL: «SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)»
	función PL/pgSQL get_seats_from_connection(integer) en la línea 15 en asignación
	sentencia SQL: «SELECT COUNT(seat)::real / (SELECT Count(*) FROM get_seats_from_connection($1))::real  FROM get_seats_from_connection($1)
					WHERE is_taken = TRUE»
	función PL/pgSQL calc_occupancy_rate(integer) en la línea 3 en asignación
2020-12-21 16:41:36.181 CET [36800] LOG:  duration: 1.686 ms  plan:
	Query Text: SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)
	Result  (cost=0.26..0.27 rows=1 width=1)
	  InitPlan 1 (returns $0)
	    ->  Result  (cost=0.00..0.26 rows=1 width=1)
2020-12-21 16:41:36.181 CET [36800] CONTEXTO:  sentencia SQL: «SELECT (
						SELECT is_seat_occupied(s.seat_id, $1)
					)»
	función PL/pgSQL get_seats_from_connection(integer) en la línea 15 en asignación
	sentencia SQL: «SELECT COUNT(seat)::real / (SELECT Count(*) FROM get_seats_from_connection($1))::real  FROM get_seats_from_connection($1)
					WHERE is_taken = TRUE»
	función PL/pgSQL calc_occupancy_rate(integer) en la línea 3 en asignación
```

# Main Query

```sql
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
	* psql -v station="'STATION'" -v day="'2020-08-22'" railway-system < transactions/transaction5.sql
	*
	* TEST VALUES:
	psql -v station="'Weinsberg East'" [DB-NAME] < transactions/transaction5.sql
	
	* NOTE: We see as result= 0.00% due to of the huge number of seat in a seat => no, it was because of rounding of the division
	*/
	WITH connections AS (
		SELECT * FROM schedule
		JOIN routes ON route = route_id
		WHERE end_station = (
			SELECT station_id FROM stations
			WHERE name = 'Weinsberg East'
		)
	)
	Select q1.nm as Origin, end_time::date AS arrival_date, end_time::time AS arrival_time, q2.rate from
	    (SELECT s.name as nm, sch.end_time, connections.connection_id as cnt
	        from stations as s, schedule as sch, routes as r, connections
	        where connections.connection_id =sch.connection_id and sch.route = r.route_id and r.start_station= s.station_id) as q1
	    Inner join
	        (SELECT cor.connection as cnt, format_to_percentage(cor.rate) as rate from connections ,calc_occupancy_rate(connections.connection_id)  as cor) as q2
	    ON q1.cnt=q2.cnt
	    ORDER BY q2.rate DESC;
	Sort  (cost=26605104383.23..26752572296.80 rows=58987165430 width=76)
	  Sort Key: q2.rate DESC
	  CTE connections
	    ->  Hash Join  (cost=801.34..1376.56 rows=10454 width=76)
	          Hash Cond: (schedule.route = routes_0.route_id)
	          InitPlan 1 (returns $0)
	            ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=4)
	                  Filter: ((name)::text = 'Weinsberg East'::text)
	          ->  Seq Scan on schedule  (cost=0.00..397.41 rows=19541 width=56)
	          ->  Hash  (cost=397.41..397.41 rows=107 width=20)
	                Buckets: 1024  Batches: 1  Memory Usage: 9kB
	                ->  Append  (cost=0.00..397.41 rows=107 width=20)
	                      ->  Seq Scan on routes_0  (cost=0.00..78.12 rows=21 width=20)
	                            Filter: (end_station = $0)
	                      ->  Seq Scan on routes_1  (cost=0.00..81.25 rows=22 width=20)
	                            Filter: (end_station = $0)
	                      ->  Seq Scan on routes_2  (cost=0.00..78.12 rows=21 width=20)
	                            Filter: (end_station = $0)
	                      ->  Seq Scan on routes_3  (cost=0.00..78.12 rows=21 width=20)
	                            Filter: (end_station = $0)
	                      ->  Seq Scan on routes_4  (cost=0.00..81.25 rows=22 width=20)
	                            Filter: (end_station = $0)
	  ->  Merge Join  (cost=2545079.12..326322412.27 rows=58987165430 width=76)
	        Merge Cond: (sch.connection_id = connections.connection_id)
	        ->  Merge Join  (cost=2544172.14..19508494.30 rows=1128509327 width=80)
	              Merge Cond: (sch.connection_id = q2.cnt)
	              ->  Sort  (cost=387560.39..392834.01 rows=2109451 width=44)
	                    Sort Key: sch.connection_id
	                    ->  Merge Join  (cost=4452.58..36192.05 rows=2109451 width=44)
	                          Merge Cond: (sch.route = r.route_id)
	                          ->  Sort  (cost=1790.12..1838.97 rows=19541 width=16)
	                                Sort Key: sch.route
	                                ->  Seq Scan on schedule sch  (cost=0.00..397.41 rows=19541 width=16)
	                          ->  Sort  (cost=2662.46..2716.44 rows=21590 width=36)
	                                Sort Key: r.route_id
	                                ->  Hash Join  (cost=600.66..1108.19 rows=21590 width=36)
	                                      Hash Cond: (r.start_station = s.station_id)
	                                      ->  Append  (cost=0.00..450.85 rows=21590 width=8)
	                                            ->  Seq Scan on routes_0 r  (cost=0.00..67.50 rows=4250 width=8)
	                                            ->  Seq Scan on routes_1 r_1  (cost=0.00..70.20 rows=4420 width=8)
	                                            ->  Seq Scan on routes_2 r_2  (cost=0.00..67.50 rows=4250 width=8)
	                                            ->  Seq Scan on routes_3 r_3  (cost=0.00..67.50 rows=4250 width=8)
	                                            ->  Seq Scan on routes_4 r_4  (cost=0.00..70.20 rows=4420 width=8)
	                                      ->  Hash  (cost=353.07..353.07 rows=19807 width=36)
	                                            Buckets: 32768  Batches: 1  Memory Usage: 1299kB
	                                            ->  Seq Scan on stations s  (cost=0.00..353.07 rows=19807 width=36)
	              ->  Materialize  (cost=2156611.76..2208881.76 rows=10454000 width=36)
	                    ->  Sort  (cost=2156611.76..2182746.76 rows=10454000 width=36)
	                          Sort Key: q2.cnt
	                          ->  Subquery Scan on q2  (cost=0.25..366099.33 rows=10454000 width=36)
	                                ->  Nested Loop  (cost=0.25..261559.33 rows=10454000 width=36)
	                                      ->  CTE Scan on connections connections_1  (cost=0.00..209.08 rows=10454 width=4)
	                                      ->  Function Scan on calc_occupancy_rate cor  (cost=0.25..10.25 rows=1000 width=8)
	        ->  Sort  (cost=906.98..933.11 rows=10454 width=4)
	              Sort Key: connections.connection_id
	              ->  CTE Scan on connections  (cost=0.00..209.08 rows=10454 width=4)
	JIT:
	  Functions: 67
	  Options: Inlining true, Optimization true, Expressions true, Deforming true
```
