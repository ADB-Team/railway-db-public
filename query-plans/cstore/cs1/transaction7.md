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

	Update on trains  (cost=963.45..1514.20 rows=10000 width=72) (actual time=52.743..52.743 rows=0 loops=1)
   ->  Hash Semi Join  (cost=963.45..1514.20 rows=10000 width=72) (actual time=18.621..28.913 rows=1448 loops=1)
         Hash Cond: (trains.producer = producers.producer_id)
         ->  Seq Scan on trains  (cost=0.00..387.00 rows=20000 width=50) (actual time=0.014..4.966 rows=20000 loops=1)
         ->  Hash  (cost=962.33..962.33 rows=90 width=22) (actual time=18.589..18.589 rows=1444 loops=1)
               Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 95kB
               ->  Hash Join  (cost=382.43..962.33 rows=90 width=22) (actual time=4.916..17.487 rows=1444 loops=1)
                     Hash Cond: (producers.city = cities.city_id)
                     ->  Seq Scan on producers  (cost=0.00..504.00 rows=20000 width=14) (actual time=0.008..6.228 rows=20000 loops=1)
                     ->  Hash  (cost=381.30..381.30 rows=90 width=16) (actual time=4.894..4.894 rows=1411 loops=1)
                           Buckets: 2048 (originally 1024)  Batches: 1 (originally 1)  Memory Usage: 85kB
                           ->  Hash Join  (cost=4.80..381.30 rows=90 width=16) (actual time=0.049..4.534 rows=1411 loops=1)
                                 Hash Cond: (cities.country = countries.country_id)
                                 ->  Seq Scan on cities  (cost=0.00..323.00 rows=20000 width=14) (actual time=0.008..2.711 rows=20000 loops=1)
                                 ->  Hash  (cost=4.79..4.79 rows=1 width=10) (actual time=0.030..0.030 rows=1 loops=1)
                                       Buckets: 1024  Batches: 1  Memory Usage: 9kB
                                       ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10) (actual time=0.012..0.027 rows=1 loops=1)
                                             Filter: ((name)::text = 'Germany'::text)
                                             Rows Removed by Filter: 222
 Planning time: 0.581 ms
 Execution time: 52.896 ms
```
