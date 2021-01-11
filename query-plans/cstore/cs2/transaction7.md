# Main Query

```sql
Query Text: /*
	* All trains produced in country X are out of order.
	* → Change train states to out_of_order, cancel connections with out_of_order.
	*
	* SCRIPT PARAMS:
	* country: the country, varchar
	*
	* EXEC SCRIPT:
	* psql -v name="'NAME'" -v country="'COUNTRY'" railway-system < transactions/transaction7.sql
	*
	* TEST VALUES:
	psql -v country="'Germany'" [DB-NAME] < transactions/transaction7.sql
	*/

	Update on trains  (cost=963.45..1514.20 rows=10000 width=72) (actual time=38.659..38.659 rows=0 loops=1)
   ->  Hash Semi Join  (cost=963.45..1514.20 rows=10000 width=72) (actual time=13.676..21.958 rows=1448 loops=1)
         Hash Cond: (trains.producer = producers.producer_id)
         ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=50) (actual time=0.019..4.367 rows=20000 loops=1)
         ->  Hash  (cost=962.33..962.33 rows=90 width=22) (actual time=13.627..13.627 rows=1444 loops=1)
               Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
               ->  Hash Join  (cost=382.43..962.33 rows=90 width=22) (actual time=7.576..13.123 rows=1444 loops=1)
                     Hash Cond: (producers.city = cities.city_id)
                     ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.009..3.335 rows=20000 loops=1)
                     ->  Hash  (cost=381.30..381.30 rows=90 width=16) (actual time=7.552..7.552 rows=1411 loops=1)
                           Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
                           ->  Hash Join  (cost=4.80..381.30 rows=90 width=16) (actual time=0.054..6.605 rows=1411 loops=1)
                                 Hash Cond: (cities.country = countries.country_id)
                                 ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14) (actual time=0.007..4.027 rows=20000 loops=1)
                                 ->  Hash  (cost=4.79..4.79 rows=1 width=10) (actual time=0.035..0.035 rows=1 loops=1)
                                       Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                       ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10) (actual time=0.011..0.028 rows=1 loops=1)
                                             Filter: ((name)::text = 'Germany'::text)
                                             Rows Removed by Filter: 222
 Planning time: 0.718 ms
 Execution time: 38.930 ms

```
