# Main Query

```sql
Query Text: /*
	* A family buys a ticket.
	* → A new row for every person with a given surname is inserted in passengers_schedule.
	*
	* SCRIPT PARAMS:
	* surname: family name, varchar
	* start_station: name of start station of connection, varchar
	* end_station: name of destination station of connection, varchar
	* train_id: code of train, varchar
	* start_time: start time and date of connection, timestamp
	*
	* EXEC SCRIPT:
	* psql -v surname="'SURNAME'" -v name="'NAME'" -v phone_number="'PHONE_NR'" -v start_station="'START'" -v end_station="'END'" -v train_id="'TRAIN'" -v start_time="'DATE & TIME'" [DB-NAME] < transactions/transaction1.sql
	*
	* TEST VALUES:
	psql -v surname="'Cooper'" -v start_station="'Carnegie Central'" -v end_station="'Bytča West'" -v train_id="'cglytjrbckkgdnfegmsjpirvqpkpsh'" -v start_time="'2003-08-13 05:01:10.879157'" [DB-NAME] < transactions/transaction1.sql
	*/
	Insert on passengers_schedule  (cost=626391.75..1626391.75 rows=50000000 width=12) (actual time=360.984..367.801 rows=1813 loops=1)
   CTE con
     ->  Function Scan on get_connection  (cost=0.25..10.25 rows=1000 width=4) (actual time=11.554..11.554 rows=1 loops=1)
   CTE free_seats
     ->  Function Scan on get_seats_from_connection train_seats  (cost=20.25..30.25 rows=500 width=4) (actual time=348.737..348.746 rows=37 loops=1)
           Filter: (NOT is_taken)
           Rows Removed by Filter: 2
           InitPlan 2 (returns $1)
             ->  CTE Scan on con  (cost=0.00..20.00 rows=1000 width=4) (actual time=0.000..0.001 rows=1 loops=1)
   CTE family
     ->  Nested Loop  (cost=0.00..626351.25 rows=50000000 width=12) (actual time=360.455..364.999 rows=1813 loops=1)
           ->  CTE Scan on con con_1  (cost=0.00..20.00 rows=1000 width=4) (actual time=11.556..11.559 rows=1 loops=1)
           ->  Materialize  (cost=0.00..1456.25 rows=50000 width=8) (actual time=348.895..353.172 rows=1813 loops=1)
                 ->  Nested Loop  (cost=0.00..1206.25 rows=50000 width=8) (actual time=348.883..352.855 rows=1813 loops=1)
                       ->  CTE Scan on free_seats  (cost=0.00..10.00 rows=500 width=4) (actual time=348.745..348.764 rows=37 loops=1)
                       ->  Materialize  (cost=0.00..571.50 rows=100 width=4) (actual time=0.003..0.102 rows=49 loops=37)
                             ->  Seq Scan on passengers  (cost=0.00..571.00 rows=100 width=4) (actual time=0.112..3.613 rows=49 loops=1)
                                   Filter: ((surname)::text = 'Cooper'::text)
                                   Rows Removed by Filter: 19951
   ->  CTE Scan on family  (cost=0.00..1000000.00 rows=50000000 width=12) (actual time=360.458..365.473 rows=1813 loops=1)
 Planning time: 0.333 ms
 Trigger for constraint passengers_schedule_connection_fkey: time=68.433 calls=1813
 Trigger for constraint passengers_schedule_passenger_fkey: time=71.008 calls=1813
 Trigger for constraint passengers_schedule_seat_fkey: time=69.856 calls=1813
 Execution time: 578.349 ms


```
