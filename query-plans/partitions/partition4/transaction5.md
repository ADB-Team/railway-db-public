# Main query 

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
        
        
        Sort  (cost=1818.70..1823.58 rows=1950 width=60) (actual time=20988.057..20988.075 rows=13 loops=1)
        Sort Key: q2.rate DESC
        Sort Method: quicksort  Memory: 26kB
        CTE connections
            ->  Hash Join  (cost=772.15..1320.57 rows=2 width=75) (actual time=11.935..21.080 rows=13 loops=1)
                Hash Cond: (schedule_0.route = routes.route_id)
                InitPlan 1 (returns $0)
                    ->  Seq Scan on stations  (cost=0.00..402.59 rows=1 width=4) (actual time=3.800..3.957 rows=1 loops=1)
                        Filter: ((name)::text = 'Weinsberg East'::text)
                        Rows Removed by Filter: 19806
                ->  Append  (cost=0.00..497.11 rows=19541 width=55) (actual time=0.013..7.779 rows=19541 loops=1)
                        ->  Seq Scan on schedule_0  (cost=0.00..78.64 rows=3864 width=55) (actual time=0.012..0.922 rows=3864 loops=1)
                        ->  Seq Scan on schedule_1  (cost=0.00..81.99 rows=3999 width=55) (actual time=0.011..0.794 rows=3999 loops=1)
                        ->  Seq Scan on schedule_2  (cost=0.00..80.12 rows=3912 width=55) (actual time=0.011..0.942 rows=3912 loops=1)
                        ->  Seq Scan on schedule_3  (cost=0.00..78.38 rows=3838 width=55) (actual time=0.014..1.128 rows=3838 loops=1)
                        ->  Seq Scan on schedule_4  (cost=0.00..80.28 rows=3928 width=55) (actual time=0.011..0.793 rows=3928 loops=1)
                ->  Hash  (cost=369.54..369.54 rows=2 width=20) (actual time=8.817..8.818 rows=4 loops=1)
                        Buckets: 1024  Batches: 1  Memory Usage: 9kB
                        ->  Seq Scan on routes  (cost=0.00..369.54 rows=2 width=20) (actual time=4.778..8.779 rows=4 loops=1)
                            Filter: (end_station = $0)
                            Rows Removed by Filter: 19559
        ->  Hash Join  (cost=96.14..391.57 rows=1950 width=60) (actual time=20987.237..20987.957 rows=13 loops=1)
                Hash Cond: (connections.connection_id = q2.cnt)
                ->  Nested Loop  (cost=0.85..217.79 rows=195 width=32) (actual time=12.052..12.732 rows=13 loops=1)
                    ->  Nested Loop  (cost=0.57..149.86 rows=195 width=20) (actual time=12.018..12.465 rows=13 loops=1)
                            ->  Nested Loop  (cost=0.28..83.16 rows=195 width=20) (actual time=11.990..12.289 rows=13 loops=1)
                                ->  CTE Scan on connections  (cost=0.00..0.04 rows=2 width=4) (actual time=11.940..11.956 rows=13 loops=1)
                                ->  Append  (cost=0.28..41.51 rows=5 width=16) (actual time=0.019..0.022 rows=1 loops=13)
                                        ->  Index Scan using schedule_0_pkey on schedule_0 sch  (cost=0.28..8.30 rows=1 width=16) (never executed)
                                            Index Cond: (connection_id = connections.connection_id)
                                        ->  Index Scan using schedule_1_pkey on schedule_1 sch_1  (cost=0.28..8.30 rows=1 width=16) (actual time=0.024..0.028 rows=1 loops=3)
                                            Index Cond: (connection_id = connections.connection_id)
                                        ->  Index Scan using schedule_2_pkey on schedule_2 sch_2  (cost=0.28..8.30 rows=1 width=16) (actual time=0.013..0.014 rows=1 loops=4)
                                            Index Cond: (connection_id = connections.connection_id)
                                        ->  Index Scan using schedule_3_pkey on schedule_3 sch_3  (cost=0.28..8.30 rows=1 width=16) (actual time=0.013..0.015 rows=1 loops=3)
                                            Index Cond: (connection_id = connections.connection_id)
                                        ->  Index Scan using schedule_4_pkey on schedule_4 sch_4  (cost=0.28..8.30 rows=1 width=16) (actual time=0.012..0.013 rows=1 loops=3)
                                            Index Cond: (connection_id = connections.connection_id)
                            ->  Index Scan using routes_pkey on routes r  (cost=0.29..0.34 rows=1 width=8) (actual time=0.010..0.010 rows=1 loops=13)
                                Index Cond: (route_id = sch.route)
                    ->  Index Scan using stations_pkey on stations s  (cost=0.29..0.35 rows=1 width=20) (actual time=0.018..0.018 rows=1 loops=13)
                            Index Cond: (station_id = r.start_station)
                ->  Hash  (cost=70.29..70.29 rows=2000 width=36) (actual time=20975.134..20975.137 rows=13 loops=1)
                    Buckets: 2048  Batches: 1  Memory Usage: 17kB
                    ->  Subquery Scan on q2  (cost=0.25..70.29 rows=2000 width=36) (actual time=1473.501..20975.080 rows=13 loops=1)
                            ->  Nested Loop  (cost=0.25..50.29 rows=2000 width=36) (actual time=1473.499..20975.057 rows=13 loops=1)
                                ->  CTE Scan on connections connections_1  (cost=0.00..0.04 rows=2 width=4) (actual time=0.001..9.210 rows=13 loops=1)
                                ->  Function Scan on calc_occupancy_rate cor  (cost=0.25..10.25 rows=1000 width=8) (actual time=1612.653..1612.653 rows=1 loops=13)
