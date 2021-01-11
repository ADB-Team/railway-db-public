                                                                   QUERY PLAN                                                                    
-------------------------------------------------------------------------------------------------------------------------------------------------
 GroupAggregate  (cost=122933.75..132838.94 rows=90047 width=43) (actual time=1698.207..2530.556 rows=38426 loops=1)
   Group Key: train_id, wagon_nr
   ->  Sort  (cost=122933.75..125184.93 rows=900472 width=39) (actual time=1698.152..2283.457 rows=900472 loops=1)
         Sort Key: train_id, wagon_nr
         Sort Method: external merge  Disk: 44128kB
         ->  Foreign Scan on seats_wagons_trains_jg  (cost=0.00..9253.12 rows=900472 width=39) (actual time=13.773..206.016 rows=900472 loops=1)
               CStore File: /var/lib/postgresql/12/main/cstore_fdw/98496/98728
               CStore File Size: 6779840
 Planning Time: 0.813 ms
 JIT:
   Functions: 6
   Options: Inlining false, Optimization false, Expressions true, Deforming true
   Timing: Generation 2.694 ms, Inlining 0.000 ms, Optimization 0.946 ms, Emission 9.044 ms, Total 12.684 ms
 Execution Time: 2587.695 ms
(14 rows)
