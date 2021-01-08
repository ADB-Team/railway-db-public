# Delete cities with country

```sql
2021-01-08 14:43:56.363 UTC [1142] LOG:  duration: 6.065 ms  plan:
	Query Text: DELETE FROM ONLY "public"."cities" WHERE $1 OPERATOR(pg_catalog.=) "country"
	Delete on cities  (cost=0.00..373.00 rows=0 width=0)
	  ->  Seq Scan on cities  (cost=0.00..373.00 rows=100 width=6)
	        Filter: (74 = country)
2021-01-08 14:43:56.363 UTC [1142] CONTEXT:  SQL statement "DELETE FROM ONLY "public"."cities" WHERE $1 OPERATOR(pg_catalog.=) "country""
```

# Delete/update entries with cities

For ever passenger/producer/station with one of deleted cities:

```sql
2021-01-08 14:43:56.369 UTC [1142] LOG:  duration: 5.432 ms  plan:
	Query Text: UPDATE ONLY "public"."passengers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city"
	Update on passengers  (cost=0.00..571.00 rows=0 width=0)
	  ->  Seq Scan on passengers  (cost=0.00..571.00 rows=100 width=174)
	        Filter: (15017 = city)
2021-01-08 14:43:56.369 UTC [1142] CONTEXT:  SQL statement "UPDATE ONLY "public"."passengers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city""
2021-01-08 14:43:56.374 UTC [1142] LOG:  duration: 4.523 ms  plan:
	Query Text: UPDATE ONLY "public"."producers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city"
	Update on producers  (cost=0.00..554.00 rows=0 width=0)
	  ->  Seq Scan on producers  (cost=0.00..554.00 rows=100 width=142)
	        Filter: (15017 = city)
2021-01-08 14:43:56.374 UTC [1142] CONTEXT:  SQL statement "UPDATE ONLY "public"."producers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city""
2021-01-08 14:43:56.378 UTC [1142] LOG:  duration: 3.259 ms  plan:
	Query Text: DELETE FROM ONLY "public"."stations" WHERE $1 OPERATOR(pg_catalog.=) "city"
	Delete on stations  (cost=0.00..402.59 rows=0 width=0)
	  ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=6)
	        Filter: (15017 = city)
2021-01-08 14:43:56.378 UTC [1142] CONTEXT:  SQL statement "DELETE FROM ONLY "public"."stations" WHERE $1 OPERATOR(pg_catalog.=) "city""
```

# Finally delete country

```sql
2021-01-08 14:43:57.459 UTC [1142] LOG:  duration: 1101.939 ms  plan:
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
	DELETE FROM countries WHERE country_name = 'Poland';
	Delete on countries  (cost=0.00..4.79 rows=0 width=0)
	  ->  Seq Scan on countries  (cost=0.00..4.79 rows=1 width=6)
	        Filter: ((country_name)::text = 'Poland'::text)
```