# Main Query
```sql
Query Text: /*
	* A country disappears. Maybe for political reasons. Whatever.
	* As a consequence, no trains can arrive or come from there anymore.
	* → All routes and thus connections and passengers' schedules are deleted.
	*
	* SCRIPT PARAMS:
	* country: name of the country to be deleted, varchar
	*
	* EXEC SCRIPT:
	* psql -v country="'COUNTRY'" railway-system < transactions/backup_transaction2.sql
	*
	* TEST VALUES:
	psql -v country="'Poland'" [DB-NAME] < transactions/backup_transaction2.sql
	*/
	Delete on countries  (cost=0.00..4.79 rows=1 width=6) (actual time=0.081..0.081 rows=0 loops=1)
   ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=6) (actual time=0.026..0.047 rows=1 loops=1)
         Filter: ((name)::text = 'Poland'::text)
         Rows Removed by Filter: 222
 Planning time: 0.352 ms
 Trigger for constraint cities_country_fkey on countries: time=5.813 calls=1
 Trigger for constraint passengers_city_fkey on cities: time=600.940 calls=200
 Trigger for constraint producers_city_fkey on cities: time=556.713 calls=200
 Trigger for constraint stations_city_fkey on cities: time=474.659 calls=200
 Execution time: 1638.739 ms


```