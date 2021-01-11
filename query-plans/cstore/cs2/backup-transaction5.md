## MAIN QUERY
```sql
Query Text: /*
	* Select stations with random criteria.
	*
	* EXEC SCRIPT:
	psql [DB-NAME] < transactions/backup_transaction5.sql
	*/
	Nested Loop  (cost=379.04..2565.67 rows=7 width=44) (actual time=48.090..6758.760 rows=3377 loops=1)
   InitPlan 1 (returns $0)
     ->  Seq Scan on countries countries_1  (cost=0.00..4.79 rows=1 width=4) (actual time=0.028..0.114 rows=1 loops=1)
           Filter: ((name)::text = 'United States'::text)
           Rows Removed by Filter: 222
   ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=4) (actual time=0.171..0.188 rows=1 loops=1)
         Filter: (country_id = $0)
         Rows Removed by Filter: 222
   ->  Nested Loop  (cost=374.25..2556.03 rows=7 width=48) (actual time=47.915..6755.595 rows=3377 loops=1)
         Join Filter: ((routes.start_station = stations.station_id) OR (routes.end_station = stations.station_id))
         Rows Removed by Join Filter: 33787693
         ->  Seq Scan on routes  (cost=0.00..369.54 rows=6521 width=12) (actual time=0.066..20.695 rows=10030 loops=1)
               Filter: (distance > 1000)
               Rows Removed by Filter: 9533
         ->  Materialize  (cost=374.25..931.22 rows=11 width=48) (actual time=0.002..0.175 rows=3369 loops=10030)
               ->  Hash Join  (cost=374.25..931.17 rows=11 width=48) (actual time=15.798..54.054 rows=3369 loops=1)
                     Hash Cond: (stations.city = cities.city_id)
                     ->  Seq Scan on stations  (cost=0.00..551.14 rows=2201 width=48) (actual time=0.062..25.592 rows=16397 loops=1)
                           Filter: ((nr_platforms > 5) AND (date_part('year'::text, (date_created)::timestamp without time zone) > '1950'::double precision))
                           Rows Removed by Filter: 3410
                     ->  Hash  (cost=373.00..373.00 rows=100 width=8) (actual time=15.573..15.573 rows=4126 loops=1)
                           Buckets: 8192 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 226kB
                           ->  Seq Scan on cities  (cost=0.00..373.00 rows=100 width=8) (actual time=0.046..11.112 rows=4126 loops=1)
                                 Filter: (country = $0)
                                 Rows Removed by Filter: 15874
 Planning time: 3.132 ms
 Execution time: 6760.713 ms

 '''

