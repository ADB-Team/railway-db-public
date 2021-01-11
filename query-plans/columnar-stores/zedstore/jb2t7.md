                                                                       QUERY PLAN                                                                       
--------------------------------------------------------------------------------------------------------------------------------------------------------
 Update on trains  (cost=932.38..1387.58 rows=1411 width=126) (actual time=50.841..50.845 rows=0 loops=1)
   ->  Hash Semi Join  (cost=932.38..1387.58 rows=1411 width=126) (actual time=17.669..26.893 rows=1448 loops=1)
         Hash Cond: (trains.producer = producers.producer_id)
         ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=49) (actual time=0.021..5.138 rows=20000 loops=1)
         ->  Hash  (cost=914.75..914.75 rows=1411 width=77) (actual time=17.601..17.604 rows=1444 loops=1)
               Buckets: 2048  Batches: 1  Memory Usage: 174kB
               ->  Hash Join  (cost=321.64..914.75 rows=1411 width=77) (actual time=8.462..17.088 rows=1444 loops=1)
                     Hash Cond: (producers.city = cities_countries_jg.city_id)
                     ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.009..5.271 rows=20000 loops=1)
                     ->  Hash  (cost=304.00..304.00 rows=1411 width=71) (actual time=8.426..8.427 rows=1411 loops=1)
                           Buckets: 2048  Batches: 1  Memory Usage: 163kB
                           ->  Foreign Scan on cities_countries_jg  (cost=0.00..304.00 rows=1411 width=71) (actual time=3.073..7.941 rows=1411 loops=1)
                                 Filter: ((country_name)::text = 'Germany'::text)
                                 Rows Removed by Filter: 18589
                                 CStore File: /var/lib/postgresql/12/main/cstore_fdw/98733/98959
                                 CStore File Size: 439907
 Planning Time: 1.927 ms
 Execution Time: 51.627 ms
(18 rows)

                                                                             QUERY PLAN                                                                             
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Delete on schedule  (cost=1440.29..1894.10 rows=459 width=85) (actual time=42.117..42.124 rows=0 loops=1)
   ->  Hash Semi Join  (cost=1440.29..1894.10 rows=459 width=85) (actual time=27.729..37.885 rows=1355 loops=1)
         Hash Cond: ((schedule.train)::text = (trains.train_id)::text)
         ->  Seq Scan on schedule  (cost=0.00..397.41 rows=19541 width=37) (actual time=0.024..4.944 rows=19541 loops=1)
         ->  Hash  (cost=1433.97..1433.97 rows=505 width=110) (actual time=27.650..27.655 rows=1448 loops=1)
               Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 220kB
               ->  Hash Join  (cost=932.38..1433.97 rows=505 width=110) (actual time=25.801..26.981 rows=1448 loops=1)
                     Hash Cond: (trains.producer = producers.producer_id)
                     ->  Seq Scan on trains  (cost=0.00..469.71 rows=7154 width=41) (actual time=0.024..5.968 rows=7611 loops=1)
                           Filter: (train_state = 'out_of_order'::train_state)
                           Rows Removed by Filter: 12389
                     ->  Hash  (cost=914.75..914.75 rows=1411 width=77) (actual time=19.222..19.226 rows=1444 loops=1)
                           Buckets: 2048  Batches: 1  Memory Usage: 174kB
                           ->  Hash Join  (cost=321.64..914.75 rows=1411 width=77) (actual time=10.122..18.743 rows=1444 loops=1)
                                 Hash Cond: (producers.city = cities_countries_jg.city_id)
                                 ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.006..5.117 rows=20000 loops=1)
                                 ->  Hash  (cost=304.00..304.00 rows=1411 width=71) (actual time=10.093..10.094 rows=1411 loops=1)
                                       Buckets: 2048  Batches: 1  Memory Usage: 163kB
                                       ->  Foreign Scan on cities_countries_jg  (cost=0.00..304.00 rows=1411 width=71) (actual time=2.440..9.353 rows=1411 loops=1)
                                             Filter: ((country_name)::text = 'Germany'::text)
                                             Rows Removed by Filter: 18589
                                             CStore File: /var/lib/postgresql/12/main/cstore_fdw/98733/98959
                                             CStore File Size: 439907
 Planning Time: 0.896 ms
 Trigger for constraint passengers_schedule_connection_fkey: time=7204.997 calls=1355
 Execution Time: 7247.907 ms
(26 rows)
