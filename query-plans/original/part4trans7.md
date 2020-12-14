# Main query

    Query Text: DELETE FROM schedule
        WHERE train IN (
            SELECT train_id FROM trains
            JOIN producers ON producer = producer_id
            JOIN cities ON city = city_id
            JOIN countries ON country = country_id
            WHERE train_state = 'out_of_order'
            AND countries.name = 'Germany'
        );
        Delete on schedule  (cost=1460.71..7754.60 rows=30 width=30) (actual time=182.553..182.585 rows=0 loops=1)
        Delete on schedule_0
        Delete on schedule_1
        Delete on schedule_2
        Delete on schedule_3
        Delete on schedule_4
        ->  Hash Semi Join  (cost=1460.71..1549.56 rows=6 width=30) (actual time=35.131..38.417 rows=250 loops=1)
                Hash Cond: ((schedule_0.train)::text = (trains.train_id)::text)
                ->  Seq Scan on schedule_0  (cost=0.00..78.64 rows=3864 width=37) (actual time=0.020..1.520 rows=3864 loops=1)
                ->  Hash  (cost=1460.31..1460.31 rows=32 width=55) (actual time=35.026..35.032 rows=1448 loops=1)
                    Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 141kB
                    ->  Hash Join  (cost=963.45..1460.31 rows=32 width=55) (actual time=32.776..34.191 rows=1448 loops=1)
                            Hash Cond: (trains.producer = producers.producer_id)
                            ->  Seq Scan on trains  (cost=0.00..469.71 rows=7154 width=41) (actual time=0.038..7.449 rows=7611 loops=1)
                                Filter: (train_state = 'out_of_order'::train_state)
                                Rows Removed by Filter: 12389
                            ->  Hash  (cost=962.33..962.33 rows=90 width=22) (actual time=24.620..24.625 rows=1444 loops=1)
                                Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
                                ->  Hash Join  (cost=382.43..962.33 rows=90 width=22) (actual time=11.654..23.732 rows=1444 loops=1)
                                        Hash Cond: (producers.city = cities.city_id)
                                        ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.008..7.200 rows=20000 loops=1)
                                        ->  Hash  (cost=381.30..381.30 rows=90 width=16) (actual time=11.584..11.587 rows=1411 loops=1)
                                            Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
                                            ->  Hash Join  (cost=4.80..381.30 rows=90 width=16) (actual time=0.168..10.812 rows=1411 loops=1)
                                                    Hash Cond: (cities.country = countries.country_id)
                                                    ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14) (actual time=0.029..6.149 rows=20000 loops=1)
                                                    ->  Hash  (cost=4.79..4.79 rows=1 width=10) (actual time=0.091..0.093 rows=1 loops=1)
                                                        Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                                        ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10) (actual time=0.034..0.074 rows=1 loops=1)
                                                                Filter: ((name)::text = 'Germany'::text)
                                                                Rows Removed by Filter: 222
        ->  Hash Semi Join  (cost=1460.71..1553.27 rows=6 width=30) (actual time=30.892..33.362 rows=301 loops=1)
                Hash Cond: ((schedule_1.train)::text = (trains.train_id)::text)
                ->  Seq Scan on schedule_1  (cost=0.00..81.99 rows=3999 width=37) (actual time=0.013..1.198 rows=3999 loops=1)
                ->  Hash  (cost=1460.31..1460.31 rows=32 width=55) (actual time=30.828..30.833 rows=1448 loops=1)
                    Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 141kB
                    ->  Hash Join  (cost=963.45..1460.31 rows=32 width=55) (actual time=27.630..29.602 rows=1448 loops=1)
                            Hash Cond: (trains.producer = producers.producer_id)
                            ->  Seq Scan on trains  (cost=0.00..469.71 rows=7154 width=41) (actual time=0.013..6.022 rows=7611 loops=1)
                                Filter: (train_state = 'out_of_order'::train_state)
                                Rows Removed by Filter: 12389
                            ->  Hash  (cost=962.33..962.33 rows=90 width=22) (actual time=21.121..21.124 rows=1444 loops=1)
                                Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
                                ->  Hash Join  (cost=382.43..962.33 rows=90 width=22) (actual time=10.214..20.489 rows=1444 loops=1)
                                        Hash Cond: (producers.city = cities.city_id)
                                        ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.008..5.807 rows=20000 loops=1)
                                        ->  Hash  (cost=381.30..381.30 rows=90 width=16) (actual time=10.172..10.173 rows=1411 loops=1)
                                            Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
                                            ->  Hash Join  (cost=4.80..381.30 rows=90 width=16) (actual time=0.104..9.480 rows=1411 loops=1)
                                                    Hash Cond: (cities.country = countries.country_id)
                                                    ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14) (actual time=0.010..5.627 rows=20000 loops=1)
                                                    ->  Hash  (cost=4.79..4.79 rows=1 width=10) (actual time=0.057..0.057 rows=1 loops=1)
                                                        Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                                        ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10) (actual time=0.018..0.051 rows=1 loops=1)
                                                                Filter: ((name)::text = 'Germany'::text)
                                                                Rows Removed by Filter: 222
        ->  Hash Semi Join  (cost=1460.71..1551.17 rows=6 width=30) (actual time=31.665..33.892 rows=290 loops=1)
                Hash Cond: ((schedule_2.train)::text = (trains.train_id)::text)
                ->  Seq Scan on schedule_2  (cost=0.00..80.12 rows=3912 width=37) (actual time=0.023..1.107 rows=3912 loops=1)
                ->  Hash  (cost=1460.31..1460.31 rows=32 width=55) (actual time=31.577..31.582 rows=1448 loops=1)
                    Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 141kB
                    ->  Hash Join  (cost=963.45..1460.31 rows=32 width=55) (actual time=29.242..30.712 rows=1448 loops=1)
                            Hash Cond: (trains.producer = producers.producer_id)
                            ->  Seq Scan on trains  (cost=0.00..469.71 rows=7154 width=41) (actual time=0.028..5.158 rows=7611 loops=1)
                                Filter: (train_state = 'out_of_order'::train_state)
                                Rows Removed by Filter: 12389
                            ->  Hash  (cost=962.33..962.33 rows=90 width=22) (actual time=23.513..23.516 rows=1444 loops=1)
                                Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
                                ->  Hash Join  (cost=382.43..962.33 rows=90 width=22) (actual time=10.159..22.665 rows=1444 loops=1)
                                        Hash Cond: (producers.city = cities.city_id)
                                        ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.015..7.629 rows=20000 loops=1)
                                        ->  Hash  (cost=381.30..381.30 rows=90 width=16) (actual time=10.099..10.101 rows=1411 loops=1)
                                            Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
                                            ->  Hash Join  (cost=4.80..381.30 rows=90 width=16) (actual time=0.117..9.448 rows=1411 loops=1)
                                                    Hash Cond: (cities.country = countries.country_id)
                                                    ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14) (actual time=0.015..5.454 rows=20000 loops=1)
                                                    ->  Hash  (cost=4.79..4.79 rows=1 width=10) (actual time=0.058..0.059 rows=1 loops=1)
                                                        Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                                        ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10) (actual time=0.018..0.052 rows=1 loops=1)
                                                                Filter: ((name)::text = 'Germany'::text)
                                                                Rows Removed by Filter: 222
        ->  Hash Semi Join  (cost=1460.71..1549.23 rows=6 width=30) (actual time=30.408..32.587 rows=272 loops=1)
                Hash Cond: ((schedule_3.train)::text = (trains.train_id)::text)
                ->  Seq Scan on schedule_3  (cost=0.00..78.38 rows=3838 width=37) (actual time=0.013..1.084 rows=3838 loops=1)
                ->  Hash  (cost=1460.31..1460.31 rows=32 width=55) (actual time=30.338..30.343 rows=1448 loops=1)
                    Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 141kB
                    ->  Hash Join  (cost=963.45..1460.31 rows=32 width=55) (actual time=27.628..29.129 rows=1448 loops=1)
                            Hash Cond: (trains.producer = producers.producer_id)
                            ->  Seq Scan on trains  (cost=0.00..469.71 rows=7154 width=41) (actual time=0.012..5.111 rows=7611 loops=1)
                                Filter: (train_state = 'out_of_order'::train_state)
                                Rows Removed by Filter: 12389
                            ->  Hash  (cost=962.33..962.33 rows=90 width=22) (actual time=21.920..21.923 rows=1444 loops=1)
                                Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
                                ->  Hash Join  (cost=382.43..962.33 rows=90 width=22) (actual time=10.406..21.254 rows=1444 loops=1)
                                        Hash Cond: (producers.city = cities.city_id)
                                        ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.016..6.573 rows=20000 loops=1)
                                        ->  Hash  (cost=381.30..381.30 rows=90 width=16) (actual time=10.351..10.353 rows=1411 loops=1)
                                            Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
                                            ->  Hash Join  (cost=4.80..381.30 rows=90 width=16) (actual time=0.115..9.739 rows=1411 loops=1)
                                                    Hash Cond: (cities.country = countries.country_id)
                                                    ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14) (actual time=0.015..5.953 rows=20000 loops=1)
                                                    ->  Hash  (cost=4.79..4.79 rows=1 width=10) (actual time=0.065..0.066 rows=1 loops=1)
                                                        Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                                        ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10) (actual time=0.021..0.055 rows=1 loops=1)
                                                                Filter: ((name)::text = 'Germany'::text)
                                                                Rows Removed by Filter: 222
        ->  Hash Semi Join  (cost=1460.71..1551.37 rows=6 width=30) (actual time=36.751..39.130 rows=242 loops=1)
                Hash Cond: ((schedule_4.train)::text = (trains.train_id)::text)
                ->  Seq Scan on schedule_4  (cost=0.00..80.28 rows=3928 width=37) (actual time=0.012..1.179 rows=3928 loops=1)
                ->  Hash  (cost=1460.31..1460.31 rows=32 width=55) (actual time=36.696..36.701 rows=1448 loops=1)
                    Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 141kB
                    ->  Hash Join  (cost=963.45..1460.31 rows=32 width=55) (actual time=33.820..35.632 rows=1448 loops=1)
                            Hash Cond: (trains.producer = producers.producer_id)
                            ->  Seq Scan on trains  (cost=0.00..469.71 rows=7154 width=41) (actual time=0.011..9.562 rows=7611 loops=1)
                                Filter: (train_state = 'out_of_order'::train_state)
                                Rows Removed by Filter: 12389
                            ->  Hash  (cost=962.33..962.33 rows=90 width=22) (actual time=22.640..22.644 rows=1444 loops=1)
                                Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
                                ->  Hash Join  (cost=382.43..962.33 rows=90 width=22) (actual time=10.270..21.811 rows=1444 loops=1)
                                        Hash Cond: (producers.city = cities.city_id)
                                        ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.015..6.544 rows=20000 loops=1)
                                        ->  Hash  (cost=381.30..381.30 rows=90 width=16) (actual time=10.226..10.228 rows=1411 loops=1)
                                            Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
                                            ->  Hash Join  (cost=4.80..381.30 rows=90 width=16) (actual time=0.137..9.598 rows=1411 loops=1)
                                                    Hash Cond: (cities.country = countries.country_id)
                                                    ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14) (actual time=0.011..5.741 rows=20000 loops=1)
                                                    ->  Hash  (cost=4.79..4.79 rows=1 width=10) (actual time=0.096..0.097 rows=1 loops=1)
                                                        Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                                        ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10) (actual time=0.039..0.087 rows=1 loops=1)
                                                                Filter: ((name)::text = 'Germany'::text)
                                                                Rows Removed by Filter: 222
