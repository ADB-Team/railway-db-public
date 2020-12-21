# Transaction 1

## Getting the seat


    Query Text: SELECT (
        SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
      ) > 0
    Result  (cost=868.89..868.90 rows=1 width=1) (actual time=7.003..7.006 rows=1 loops=1)
      InitPlan 1 (returns $0)
        ->  Aggregate  (cost=868.88..868.89 rows=1 width=8) (actual time=6.999..7.002 rows=1 loops=1)
              ->  Append  (cost=0.00..868.86 rows=5 width=0) (actual time=6.996..6.997 rows=0 loops=1)
                    ->  Seq Scan on passengers_schedule_0  (cost=0.00..174.58 rows=1 width=0) (actual time=1.435..1.435 rows=0 loops=1)
                          Filter: ((seat = $1) AND (connection = $2))
                          Rows Removed by Filter: 7950
                    ->  Seq Scan on passengers_schedule_1  (cost=0.00..174.58 rows=1 width=0) (actual time=1.382..1.382 rows=0 loops=1)
                          Filter: ((seat = $1) AND (connection = $2))
                          Rows Removed by Filter: 7778
                    ->  Seq Scan on passengers_schedule_2  (cost=0.00..174.58 rows=1 width=0) (actual time=1.425..1.425 rows=0 loops=1)
                          Filter: ((seat = $1) AND (connection = $2))
                          Rows Removed by Filter: 7908
                    ->  Seq Scan on passengers_schedule_3  (cost=0.00..170.52 rows=1 width=0) (actual time=1.378..1.379 rows=0 loops=1)
                          Filter: ((seat = $1) AND (connection = $2))
                          Rows Removed by Filter: 7668
                    ->  Seq Scan on passengers_schedule_4  (cost=0.00..174.58 rows=1 width=0) (actual time=1.372..1.372 rows=0 loops=1)
                          Filter: ((seat = $1) AND (connection = $2))
                          Rows Removed by Filter: 7796
                          
     
 # Transaction 5
     
 ## for every connection 
     
        Query Text: SELECT (
			SELECT COUNT(*) FROM passengers_schedule WHERE seat = $1 AND connection = $2
		) > 0
	Result  (cost=905.43..905.44 rows=1 width=1) (actual time=7.347..7.350 rows=1 loops=1)
	  InitPlan 1 (returns $0)
	    ->  Aggregate  (cost=905.42..905.43 rows=1 width=8) (actual time=7.344..7.347 rows=1 loops=1)
	          ->  Append  (cost=0.00..905.40 rows=5 width=0) (actual time=7.341..7.343 rows=0 loops=1)
	                ->  Seq Scan on passengers_schedule_0  (cost=0.00..182.70 rows=1 width=0) (actual time=1.463..1.463 rows=0 loops=1)
	                      Filter: ((seat = $1) AND (connection = $2))
	                      Rows Removed by Filter: 8316
	                ->  Seq Scan on passengers_schedule_1  (cost=0.00..178.64 rows=1 width=0) (actual time=1.603..1.603 rows=0 loops=1)
	                      Filter: ((seat = $1) AND (connection = $2))
	                      Rows Removed by Filter: 8127
	                ->  Seq Scan on passengers_schedule_2  (cost=0.00..182.70 rows=1 width=0) (actual time=1.434..1.434 rows=0 loops=1)
	                      Filter: ((seat = $1) AND (connection = $2))
	                      Rows Removed by Filter: 8260
	                ->  Seq Scan on passengers_schedule_3  (cost=0.00..178.64 rows=1 width=0) (actual time=1.418..1.418 rows=0 loops=1)
	                      Filter: ((seat = $1) AND (connection = $2))
	                      Rows Removed by Filter: 8042
	                ->  Seq Scan on passengers_schedule_4  (cost=0.00..182.70 rows=1 width=0) (actual time=1.421..1.421 rows=0 loops=1)
	                      Filter: ((seat = $1) AND (connection = $2))
	                      Rows Removed by Filter: 8168
# Transaction 7

## Delete from passengers_schedule, for every connection with this train:

    Query Text: DELETE FROM "public"."passengers_schedule" WHERE $1 OPERATOR(pg_catalog.=) "connection"
      Delete on passengers_schedule  (cost=0.00..734.41 rows=10 width=6) (actual time=7.733..7.735 rows=0 loops=1)
        Delete on passengers_schedule_0
        Delete on passengers_schedule_1
        Delete on passengers_schedule_2
        Delete on passengers_schedule_3
        Delete on passengers_schedule_4
        ->  Seq Scan on passengers_schedule_0  (cost=0.00..148.95 rows=2 width=6) (actual time=0.150..2.017 rows=4 loops=1)
              Filter: ($1 = connection)
              Rows Removed by Filter: 7762
        ->  Seq Scan on passengers_schedule_1  (cost=0.00..145.59 rows=2 width=6) (actual time=1.411..1.411 rows=0 loops=1)
              Filter: ($1 = connection)
              Rows Removed by Filter: 7591
        ->  Seq Scan on passengers_schedule_2  (cost=0.00..148.25 rows=2 width=6) (actual time=1.494..1.494 rows=0 loops=1)
              Filter: ($1 = connection)
              Rows Removed by Filter: 7730
        ->  Seq Scan on passengers_schedule_3  (cost=0.00..144.53 rows=2 width=6) (actual time=0.297..1.370 rows=4 loops=1)
              Filter: ($1 = connection)
              Rows Removed by Filter: 7552
        ->  Seq Scan on passengers_schedule_4  (cost=0.00..147.10 rows=2 width=6) (actual time=1.381..1.381 rows=0 loops=1)
              Filter: ($1 = connection)
              Rows Removed by Filter: 7614
    2020-12-21 16:07:00.580 CET [4796] elliot@elliot2 CONTEXT:  SQL statement "DELETE FROM "public"."passengers_schedule" WHERE $1 OPERATOR(pg_catalog.=) "connection""
    2020-12-21 16:07:00.581 CET [4796] elliot@elliot2 LOG:  duration: 26845.009 ms  plan:
      Query Text: DELETE FROM schedule
      WHERE train IN (
        SELECT train_id FROM trains
        JOIN producers ON producer = producer_id
        JOIN cities ON city = city_id
        JOIN countries ON country = country_id
        WHERE train_state = 'out_of_order'
        AND countries.name = 'Germany'
      );
      Delete on schedule  (cost=1460.71..2312.04 rows=50 width=30) (actual time=126.004..126.014 rows=0 loops=1)
        ->  Hash Semi Join  (cost=1460.71..2312.04 rows=50 width=30) (actual time=67.935..114.708 rows=1355 loops=1)
              Hash Cond: ((schedule.train)::text = (trains.train_id)::text)
              ->  Seq Scan on schedule  (cost=0.00..753.51 rows=37051 width=37) (actual time=0.046..21.241 rows=37063 loops=1)
              ->  Hash  (cost=1460.31..1460.31 rows=32 width=55) (actual time=67.734..67.741 rows=1448 loops=1)
                    Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 141kB
                    ->  Hash Join  (cost=963.45..1460.31 rows=32 width=55) (actual time=61.506..65.494 rows=1448 loops=1)
                          Hash Cond: (trains.producer = producers.producer_id)
                          ->  Seq Scan on trains  (cost=0.00..469.71 rows=7154 width=41) (actual time=0.044..13.745 rows=7611 loops=1)
                                Filter: (train_state = 'out_of_order'::train_state)
                                Rows Removed by Filter: 12389
                          ->  Hash  (cost=962.33..962.33 rows=90 width=22) (actual time=47.037..47.042 rows=1444 loops=1)
                                Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
                                ->  Hash Join  (cost=382.43..962.33 rows=90 width=22) (actual time=23.582..45.246 rows=1444 loops=1)
                                      Hash Cond: (producers.city = cities.city_id)
                                      ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.015..12.921 rows=20000 loops=1)
                                      ->  Hash  (cost=381.30..381.30 rows=90 width=16) (actual time=23.536..23.539 rows=1411 loops=1)
                                            Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
                                            ->  Hash Join  (cost=4.80..381.30 rows=90 width=16) (actual time=0.147..21.990 rows=1411 loops=1)
                                                  Hash Cond: (cities.country = countries.country_id)
                                                  ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14) (actual time=0.019..10.020 rows=20000 loops=1)
                                                  ->  Hash  (cost=4.79..4.79 rows=1 width=10) (actual time=0.100..0.101 rows=1 loops=1)
                                                        Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                                        ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10) (actual time=0.032..0.094 rows=1 loops=1)
                                                              Filter: ((name)::text = 'Germany'::text)
                                                              Rows Removed by Filter: 222                          
