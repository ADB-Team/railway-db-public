## MAIN QUERY
```sql
Query Text: /*
	* Select stations with random criteria.
	*
	* EXEC SCRIPT:
	psql [DB-NAME] < transactions/backup_transaction5.sql
	*/
	Nested Loop  (cost=379.04..2465.87 rows=7 width=44) (actual time=13.657..6593.059 rows=3377 loops=1)
   InitPlan 1 (returns $0)
     ->  Seq Scan on countries countries_1  (cost=0.00..4.79 rows=1 width=4) (actual time=0.011..0.045 rows=1 loops=1)
           Filter: ((name)::text = 'United States'::text)
           Rows Removed by Filter: 222
   ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=4) (actual time=0.067..0.095 rows=1 loops=1)
         Filter: (country_id = $0)
         Rows Removed by Filter: 222
   ->  Nested Loop  (cost=374.25..2456.23 rows=7 width=48) (actual time=13.519..6590.186 rows=3377 loops=1)
         Join Filter: ((routes_cs.start_station = stations.station_id) OR (routes_cs.end_station = stations.station_id))
         Rows Removed by Join Filter: 33787693
         ->  Foreign Scan on routes_cs  (cost=0.00..269.74 rows=6521 width=12) (actual time=1.005..24.387 rows=10030 loops=1)
               Filter: (distance > 1000)
               Rows Removed by Filter: 9533
               CStore File: /var/lib/postgres/data/cstore_fdw/64685/64905
               CStore File Size: 342907
         ->  Materialize  (cost=374.25..931.22 rows=11 width=48) (actual time=0.001..0.167 rows=3369 loops=10030)
               ->  Hash Join  (cost=374.25..931.17 rows=11 width=48) (actual time=6.329..13.780 rows=3369 loops=1)
                     Hash Cond: (stations.city = cities.city_id)
                     ->  Seq Scan on stations  (cost=0.00..551.14 rows=2201 width=48) (actual time=0.267..5.466 rows=16397 loops=1)
                           Filter: ((nr_platforms > 5) AND (date_part('year'::text, (date_created)::timestamp without time zone) > '1950'::double precision))
                           Rows Removed by Filter: 3410
                     ->  Hash  (cost=373.00..373.00 rows=100 width=8) (actual time=5.885..5.885 rows=4126 loops=1)
                           Buckets: 8192 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 226kB
                           ->  Seq Scan on cities  (cost=0.00..373.00 rows=100 width=8) (actual time=0.073..4.364 rows=4126 loops=1)
                                 Filter: (country = $0)
                                 Rows Removed by Filter: 15874
 Planning time: 1.031 ms
 Execution time: 6594.833 ms
 '''

