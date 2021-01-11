# Query plan

      Sort  (cost=1317.45..1317.50 rows=20 width=60) (actual time=12215.226..12215.234 rows=13 loops=1)
      Sort Key: ((to_char(('100'::double precision * cor.rate), '999D99%'::text))::character varying) DESC
      Sort Method: quicksort  Memory: 26kB
      CTE connections
        ->  Hash Join  (cost=772.15..1220.87 rows=2 width=75) (actual time=7.237..11.868 rows=13 loops=1)
              Hash Cond: (schedule.route = routes.route_id)
              InitPlan 1 (returns $0)
                ->  Seq Scan on stations  (cost=0.00..402.59 rows=1 width=4) (actual time=3.500..3.616 rows=1 loops=1)
                      Filter: ((name)::text = 'Weinsberg East'::text)
                      Rows Removed by Filter: 19806
              ->  Seq Scan on schedule  (cost=0.00..397.41 rows=19541 width=55) (actual time=0.008..2.600 rows=19541 loops=1)
              ->  Hash  (cost=369.54..369.54 rows=2 width=20) (actual time=6.276..6.276 rows=4 loops=1)
                    Buckets: 1024  Batches: 1  Memory Usage: 9kB
                    ->  Seq Scan on routes  (cost=0.00..369.54 rows=2 width=20) (actual time=4.075..6.262 rows=4 loops=1)
                          Filter: (end_station = $0)
                          Rows Removed by Filter: 19559
      ->  Hash Join  (cost=18.31..96.15 rows=20 width=60) (actual time=876.138..12215.129 rows=13 loops=1)
            Hash Cond: (cor.connection = connections.connection_id)
            ->  Nested Loop  (cost=0.25..50.29 rows=2000 width=36) (actual time=871.217..12210.137 rows=13 loops=1)
                  ->  CTE Scan on connections connections_1  (cost=0.00..0.04 rows=2 width=4) (actual time=7.243..7.255 rows=13 loops=1)
                  ->  Function Scan on calc_occupancy_rate cor  (cost=0.25..10.25 rows=1000 width=8) (actual time=938.658..938.658 rows=1 loops=13)
            ->  Hash  (cost=18.03..18.03 rows=2 width=32) (actual time=4.908..4.910 rows=13 loops=1)
                  Buckets: 1024  Batches: 1  Memory Usage: 9kB
                  ->  Nested Loop  (cost=0.86..18.03 rows=2 width=32) (actual time=0.046..4.896 rows=13 loops=1)
                        ->  Nested Loop  (cost=0.57..17.33 rows=2 width=20) (actual time=0.036..4.835 rows=13 loops=1)
                              ->  Nested Loop  (cost=0.29..16.65 rows=2 width=20) (actual time=0.023..4.769 rows=13 loops=1)
                                    ->  CTE Scan on connections  (cost=0.00..0.04 rows=2 width=4) (actual time=0.001..4.650 rows=13 loops=1)
                                    ->  Index Scan using schedule_pkey on schedule sch  (cost=0.29..8.30 rows=1 width=16) (actual time=0.007..0.007 rows=1 loops=13)
                                          Index Cond: (connection_id = connections.connection_id)
                              ->  Index Scan using routes_pkey on routes r  (cost=0.29..0.34 rows=1 width=8) (actual time=0.004..0.004 rows=1 loops=13)
                                    Index Cond: (route_id = sch.route)
                        ->  Index Scan using stations_pkey on stations s  (cost=0.29..0.35 rows=1 width=20) (actual time=0.003..0.003 rows=1 loops=13)
                              Index Cond: (station_id = r.start_station)
    Planning Time: 2.309 ms
    Execution Time: 12216.567 ms
