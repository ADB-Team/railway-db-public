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
	Sort  (cost=20136767188.79..20248863987.86 rows=44838719625 width=76) (actual time=7980.971..7980.972 rows=13 loops=1)
   Sort Key: q2.rate DESC
   Sort Method: quicksort  Memory: 26kB
   CTE connections
     ->  Hash Join  (cost=690.35..1476.62 rows=9575 width=76) (actual time=7.346..11.469 rows=13 loops=1)
           Hash Cond: (schedule.route = routes_cs.route_id)
           InitPlan 1 (returns $0)
             ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=4) (actual time=1.785..1.905 rows=1 loops=1)
                   Filter: ((name)::text = 'Weinsberg East'::text)
                   Rows Removed by Filter: 19806
           ->  Seq Scan on schedule  (cost=0.00..397.41 rows=19541 width=56) (actual time=0.015..2.803 rows=19541 loops=1)
           ->  Hash  (cost=286.54..286.54 rows=98 width=20) (actual time=6.185..6.185 rows=4 loops=1)
                 Buckets: 1024  Batches: 1  Memory Usage: 9kB
                 ->  Foreign Scan on routes_cs  (cost=0.00..286.54 rows=98 width=20) (actual time=3.174..6.124 rows=4 loops=1)
                       Filter: (end_station = $0)
                       Rows Removed by Filter: 19559
                       CStore File: /var/lib/postgres/data/cstore_fdw/63741/63961
                       CStore File Size: 342907
   ->  Merge Join  (cost=2319874.26..249820882.07 rows=44838719625 width=76) (actual time=7978.492..7980.934 rows=13 loops=1)
         Merge Cond: (sch.connection_id = connections.connection_id)
         ->  Merge Join  (cost=2319049.61..16401224.13 rows=936578667 width=80) (actual time=7978.479..7980.912 rows=13 loops=1)
               Merge Cond: (sch.connection_id = q2.cnt)
               ->  Sort  (cost=349836.35..354614.85 rows=1911403 width=44) (actual time=49.765..51.223 rows=19112 loops=1)
                     Sort Key: sch.connection_id
                     Sort Method: quicksort  Memory: 2299kB
                     ->  Merge Join  (cost=4049.00..32817.75 rows=1911403 width=44) (actual time=33.288..43.972 rows=19541 loops=1)
                           Merge Cond: (sch.route = r.route_id)
                           ->  Sort  (cost=1790.12..1838.97 rows=19541 width=16) (actual time=10.999..13.213 rows=19541 loops=1)
                                 Sort Key: sch.route
                                 Sort Method: quicksort  Memory: 1684kB
                                 ->  Seq Scan on schedule sch  (cost=0.00..397.41 rows=19541 width=16) (actual time=0.028..4.378 rows=19541 loops=1)
                           ->  Sort  (cost=2258.89..2307.79 rows=19563 width=36) (actual time=22.284..23.792 rows=26728 loops=1)
                                 Sort Key: r.route_id
                                 Sort Method: quicksort  Memory: 2211kB
                                 ->  Hash Join  (cost=600.66..864.45 rows=19563 width=36) (actual time=10.829..18.892 rows=19563 loops=1)
                                       Hash Cond: (r.start_station = s.station_id)
                                       ->  Foreign Scan on routes_cs r  (cost=0.00..212.43 rows=19563 width=8) (actual time=0.473..2.878 rows=19563 loops=1)
                                             CStore File: /var/lib/postgres/data/cstore_fdw/63741/63961
                                             CStore File Size: 342907
                                       ->  Hash  (cost=353.07..353.07 rows=19807 width=36) (actual time=10.194..10.194 rows=19807 loops=1)
                                             Buckets: 32768  Batches: 1  Memory Usage: 1299kB
                                             ->  Seq Scan on stations s  (cost=0.00..353.07 rows=19807 width=36) (actual time=0.013..3.759 rows=19807 loops=1)
               ->  Materialize  (cost=1969213.27..2017088.27 rows=9575000 width=36) (actual time=7928.213..7928.216 rows=13 loops=1)
                     ->  Sort  (cost=1969213.27..1993150.77 rows=9575000 width=36) (actual time=7928.210..7928.211 rows=13 loops=1)
                           Sort Key: q2.cnt
                           Sort Method: quicksort  Memory: 25kB
                           ->  Subquery Scan on q2  (cost=0.25..335316.75 rows=9575000 width=36) (actual time=512.848..7928.179 rows=13 loops=1)
                                 ->  Nested Loop  (cost=0.25..239566.75 rows=9575000 width=36) (actual time=512.847..7928.164 rows=13 loops=1)
                                       ->  CTE Scan on connections connections_1  (cost=0.00..191.50 rows=9575 width=4) (actual time=7.361..11.533 rows=13 loops=1)
                                       ->  Function Scan on calc_occupancy_rate cor  (cost=0.25..10.25 rows=1000 width=8) (actual time=608.958..608.959 rows=1 loops=13)
         ->  Sort  (cost=824.65..848.59 rows=9575 width=4) (actual time=0.010..0.011 rows=13 loops=1)
               Sort Key: connections.connection_id
               Sort Method: quicksort  Memory: 25kB
               ->  CTE Scan on connections  (cost=0.00..191.50 rows=9575 width=4) (actual time=0.001..0.003 rows=13 loops=1)
 Planning time: 1.071 ms
 Execution time: 7982.559 ms
```
