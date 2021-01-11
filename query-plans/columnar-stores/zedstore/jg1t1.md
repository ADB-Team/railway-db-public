# Query Plan   
   
     Insert on passengers_schedule  (cost=40.50..307259.12 rows=24500000 width=12) (actual time=447.788..447.791 rows=0 loops=1)
      CTE con
        ->  Function Scan on get_connection  (cost=0.25..10.25 rows=1000 width=4) (actual time=27.162..27.162 rows=1 loops=1)
      CTE free_seats
        ->  Function Scan on get_seats_from_connection train_seats  (cost=20.25..30.25 rows=500 width=4) (actual time=420.612..420.612 rows=0 loops=1)
              Filter: (NOT is_taken)
              Rows Removed by Filter: 39
              InitPlan 2 (returns $1)
                ->  CTE Scan on con con_1  (cost=0.00..20.00 rows=1000 width=4) (actual time=0.000..0.001 rows=1 loops=1)
      ->  Nested Loop  (cost=0.00..307218.62 rows=24500000 width=12) (actual time=447.786..447.787 rows=0 loops=1)
            ->  CTE Scan on con  (cost=0.00..20.00 rows=1000 width=4) (actual time=27.165..27.166 rows=1 loops=1)
            ->  Materialize  (cost=0.00..1009.87 rows=24500 width=8) (actual time=420.617..420.618 rows=0 loops=1)
                  ->  Nested Loop  (cost=0.00..887.37 rows=24500 width=8) (actual time=420.614..420.615 rows=0 loops=1)
                        ->  CTE Scan on free_seats  (cost=0.00..10.00 rows=500 width=4) (actual time=420.613..420.613 rows=0 loops=1)
                        ->  Materialize  (cost=0.00..571.25 rows=49 width=4) (never executed)
                              ->  Seq Scan on passengers  (cost=0.00..571.00 rows=49 width=4) (never executed)
                                    Filter: ((surname)::text = 'Cooper'::text)
    Planning Time: 1.598 ms
    JIT:
      Functions: 20
      Options: Inlining false, Optimization false, Expressions true, Deforming true
      Timing: Generation 4.910 ms, Inlining 0.000 ms, Optimization 0.856 ms, Emission 13.078 ms, Total 18.843 ms
    Execution Time: 497.890 ms
