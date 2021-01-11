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
	Sort  (cost=12154.69..12274.74 rows=48020 width=76) (actual time=6791.887..6791.888 rows=13 loops=1)
   Sort Key: ((to_char(('100'::double precision * cor.rate), '999D99%'::text))::character varying) DESC
   Sort Method: quicksort  Memory: 26kB
   CTE connections
     ->  Hash Join  (cost=773.35..1222.07 rows=98 width=76) (actual time=4.077..7.369 rows=13 loops=1)
           Hash Cond: (schedule.route = routes.route_id)
           InitPlan 1 (returns $0)
             ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=4) (actual time=1.720..1.790 rows=1 loops=1)
                   Filter: ((name)::text = 'Weinsberg East'::text)
                   Rows Removed by Filter: 19806
           ->  Seq Scan on schedule  (cost=0.00..397.41 rows=19541 width=56) (actual time=0.023..1.983 rows=19541 loops=1)
           ->  Hash  (cost=369.54..369.54 rows=98 width=20) (actual time=3.579..3.579 rows=4 loops=1)
                 Buckets: 1024  Batches: 1  Memory Usage: 9kB
                 ->  Seq Scan on routes  (cost=0.00..369.54 rows=98 width=20) (actual time=2.102..3.573 rows=4 loops=1)
                       Filter: (end_station = $0)
                       Rows Removed by Filter: 19559
   ->  Hash Join  (cost=543.99..5063.75 rows=48020 width=76) (actual time=654.292..6791.807 rows=13 loops=1)
         Hash Cond: (cor.connection = connections.connection_id)
         ->  Nested Loop  (cost=0.25..2452.21 rows=98000 width=36) (actual time=643.045..6780.517 rows=13 loops=1)
               ->  CTE Scan on connections connections_1  (cost=0.00..1.96 rows=98 width=4) (actual time=4.080..4.091 rows=13 loops=1)
               ->  Function Scan on calc_occupancy_rate cor  (cost=0.25..10.25 rows=1000 width=8) (actual time=521.254..521.255 rows=1 loops=13)
         ->  Hash  (cost=542.51..542.51 rows=98 width=48) (actual time=11.229..11.229 rows=13 loops=1)
               Buckets: 1024  Batches: 1  Memory Usage: 9kB
               ->  Nested Loop  (cost=3.76..542.51 rows=98 width=48) (actual time=4.313..11.140 rows=13 loops=1)
                     ->  Nested Loop  (cost=3.47..508.37 rows=98 width=20) (actual time=4.254..10.748 rows=13 loops=1)
                           ->  Hash Join  (cost=3.19..474.85 rows=98 width=20) (actual time=4.021..9.902 rows=13 loops=1)
                                 Hash Cond: (sch.connection_id = connections.connection_id)
                                 ->  Seq Scan on schedule sch  (cost=0.00..397.41 rows=19541 width=16) (actual time=0.007..3.547 rows=19541 loops=1)
                                 ->  Hash  (cost=1.96..1.96 rows=98 width=4) (actual time=3.323..3.323 rows=13 loops=1)
                                       Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                       ->  CTE Scan on connections  (cost=0.00..1.96 rows=98 width=4) (actual time=0.001..3.313 rows=13 loops=1)
                           ->  Index Scan using routes_pkey on routes r  (cost=0.29..0.34 rows=1 width=8) (actual time=0.053..0.053 rows=1 loops=13)
                                 Index Cond: (route_id = sch.route)
                     ->  Index Scan using stations_pkey on stations s  (cost=0.29..0.35 rows=1 width=36) (actual time=0.023..0.023 rows=1 loops=13)
                           Index Cond: (station_id = r.start_station)
 Planning time: 1.541 ms
 Execution time: 6793.586 ms

```
