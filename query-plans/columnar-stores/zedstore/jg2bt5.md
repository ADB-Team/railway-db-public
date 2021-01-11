# QUERY PLAN

    Nested Loop  (cost=273.96..5784.57 rows=26 width=28) (actual time=15.829..8947.565 rows=3377 loops=1)
       Join Filter: ((routes.start_station = stations.station_id) OR (routes.end_station = stations.station_id))
       Rows Removed by Join Filter: 33787693
       InitPlan 1 (returns $0)
         ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=4) (actual time=0.019..0.052 rows=1 loops=1)
               Filter: ((name)::text = 'United States'::text)
               Rows Removed by Filter: 222
       ->  Seq Scan on routes  (cost=0.00..369.54 rows=10042 width=12) (actual time=0.025..8.283 rows=10030 loops=1)
             Filter: (distance > 1000)
             Rows Removed by Filter: 9533
       ->  Materialize  (cost=269.18..841.20 rows=26 width=28) (actual time=0.001..0.274 rows=3369 loops=10030)
             ->  Hash Join  (cost=269.18..841.07 rows=26 width=28) (actual time=5.296..17.592 rows=3369 loops=1)
                   Hash Cond: (stations.city = cities_countries_jg.city_id)
                   ->  Seq Scan on stations  (cost=0.00..551.14 rows=5466 width=32) (actual time=0.022..8.551 rows=16397 loops=1)
                         Filter: ((nr_platforms > 5) AND (date_part('year'::text, (date_created)::timestamp without time zone) > '1950'::double precision))
                         Rows Removed by Filter: 3410
                   ->  Hash  (cost=268.00..268.00 rows=94 width=4) (actual time=5.203..5.205 rows=4126 loops=1)
                         Buckets: 8192 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 210kB
                         ->  Foreign Scan on cities_countries_jg  (cost=0.00..268.00 rows=94 width=4) (actual time=0.747..3.820 rows=4126 loops=1)
                               Filter: (country_id = $0)
                               Rows Removed by Filter: 15874
                               CStore File: /var/lib/postgresql/12/main/cstore_fdw/98733/98959
                               CStore File Size: 439907
     Planning Time: 1.697 ms
     Execution Time: 8948.484 ms
