# Main Query

```sql
Query Text: /*
	* My dream comes true and Görlitz finally gets a regular connection to Berlin.
	* → Add new connection for every hour for one year from today on.
	*
	* SCRIPT PARAMS:
	* train_id: code of the train, varchar
	*
	* EXEC SCRIPT:
	* psql -v start_station="'START'" -v end_station="'END'" -v start_time="'DATE TIME'" -v train_id="'TRAIN'" [DB-NAME] < transactions/transaction3.sql
	*
	* TEST VALUES:
	psql -v train_id="'bywdfevctetzjdwvofemzctyerwshr'" [DB-NAME] < transactions/transaction3.sql
	*/

	Insert on stations  (cost=373.00..373.02 rows=1 width=48) (actual time=4.388..4.388 rows=0 loops=1)
   InitPlan 1 (returns $0)
     ->  Seq Scan on cities  (cost=0.00..373.00 rows=100 width=4) (actual time=1.357..3.795 rows=1 loops=1)
           Filter: ((name)::text = 'Görlitz'::text)
           Rows Removed by Filter: 19999
   ->  Result  (cost=0.00..0.02 rows=1 width=48) (actual time=4.239..4.240 rows=1 loops=1)
 Planning time: 0.673 ms
 Trigger for constraint stations_city_fkey: time=0.526 calls=1
 Execution time: 5.443 ms

```
	