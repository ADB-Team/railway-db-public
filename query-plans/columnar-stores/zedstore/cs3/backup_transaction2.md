# Delete cities from to-be-deleted country

```sql
2021-01-08 14:11:25.933 UTC [578] LOG:  duration: 5.573 ms  plan:
	Query Text: DELETE FROM ONLY "public"."cities" WHERE $1 OPERATOR(pg_catalog.=) "country"
	Delete on cities  (cost=0.00..373.00 rows=0 width=0)
	  ->  Seq Scan on cities  (cost=0.00..373.00 rows=100 width=6)
	        Filter: (74 = country)
2021-01-08 14:11:25.933 UTC [578] CONTEXT:  SQL statement "DELETE FROM ONLY "public"."cities" WHERE $1 OPERATOR(pg_catalog.=) "country""
```

# Delete/Update entries with cities

For every passenger, producer or station with one of deleted cities:

```sql
2021-01-08 14:11:25.939 UTC [578] LOG:  duration: 5.310 ms  plan:
	Query Text: UPDATE ONLY "public"."passengers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city"
	Update on passengers  (cost=0.00..571.00 rows=0 width=0)
	  ->  Seq Scan on passengers  (cost=0.00..571.00 rows=100 width=174)
	        Filter: (15017 = city)
2021-01-08 14:11:25.939 UTC [578] CONTEXT:  SQL statement "UPDATE ONLY "public"."passengers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city""
2021-01-08 14:11:25.944 UTC [578] LOG:  duration: 4.724 ms  plan:
	Query Text: UPDATE ONLY "public"."producers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city"
	Update on producers  (cost=0.00..554.00 rows=0 width=0)
	  ->  Seq Scan on producers  (cost=0.00..554.00 rows=100 width=142)
	        Filter: (15017 = city)
2021-01-08 14:11:25.944 UTC [578] CONTEXT:  SQL statement "UPDATE ONLY "public"."producers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city""
2021-01-08 14:11:25.947 UTC [578] LOG:  duration: 2.700 ms  plan:
	Query Text: DELETE FROM ONLY "public"."stations" WHERE $1 OPERATOR(pg_catalog.=) "city"
	Delete on stations  (cost=0.00..402.59 rows=0 width=0)
	  ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=6)
	        Filter: (15017 = city)
2021-01-08 14:11:25.947 UTC [578] CONTEXT:  SQL statement "DELETE FROM ONLY "public"."stations" WHERE $1 OPERATOR(pg_catalog.=) "city""
```

# Main Query - Delete country

```sql
2021-01-08 14:11:27.053 UTC [578] LOG:  duration: 1126.306 ms  plan:
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