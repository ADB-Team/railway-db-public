# Delete cities with given country

```sql
2021-01-07 13:42:59.017 UTC [916] LOG:  duration: 12.616 ms  plan:
	Query Text: DELETE FROM ONLY "public"."cities" WHERE $1 OPERATOR(pg_catalog.=) "country"
	Delete on cities  (cost=0.00..373.00 rows=0 width=0)
	  ->  Seq Scan on cities  (cost=0.00..373.00 rows=100 width=6)
	        Filter: (74 = country)
2021-01-07 13:42:59.017 UTC [916] CONTEXT:  SQL statement "DELETE FROM ONLY "public"."cities" WHERE $1 OPERATOR(pg_catalog.=) "country""
2021-01-07 13:42:59.025 UTC [916] LOG:  duration: 7.036 ms  plan:
	Query Text: UPDATE ONLY "public"."passengers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city"
	Update on passengers  (cost=0.00..571.00 rows=0 width=0)
	  ->  Seq Scan on passengers  (cost=0.00..571.00 rows=100 width=174)
	        Filter: (15017 = city)
```

# Delete/Update all passengers, 
```sql
2021-01-07 13:42:59.025 UTC [916] CONTEXT:  SQL statement "UPDATE ONLY "public"."passengers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city""
2021-01-07 13:42:59.032 UTC [916] LOG:  duration: 6.683 ms  plan:
	Query Text: UPDATE ONLY "public"."producers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city"
	Update on producers  (cost=0.00..554.00 rows=0 width=0)
	  ->  Seq Scan on producers  (cost=0.00..554.00 rows=100 width=142)
	        Filter: (15017 = city)
2021-01-07 13:42:59.032 UTC [916] CONTEXT:  SQL statement "UPDATE ONLY "public"."producers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city""
2021-01-07 13:42:59.036 UTC [916] LOG:  duration: 3.452 ms  plan:
	Query Text: DELETE FROM ONLY "public"."stations" WHERE $1 OPERATOR(pg_catalog.=) "city"
	Delete on stations  (cost=0.00..402.59 rows=0 width=0)
	  ->  Seq Scan on stations  (cost=0.00..402.59 rows=99 width=6)
	        Filter: (15017 = city)
2021-01-07 13:42:59.036 UTC [916] CONTEXT:  SQL statement "DELETE FROM ONLY "public"."stations" WHERE $1 OPERATOR(pg_catalog.=) "city""
2021-01-07 13:42:59.038 UTC [916] LOG:  duration: 2.103 ms  plan:
	Query Text: UPDATE ONLY "public"."passengers" SET "city" = NULL WHERE $1 OPERATOR(pg_catalog.=) "city"
	Update on passengers  (cost=0.00..571.00 rows=0 width=0)
	  ->  Seq Scan on passengers  (cost=0.00..571.00 rows=100 width=174)
	        Filter: (15051 = city)
```

# Main Query

```sql
2021-01-07 13:43:00.161 UTC [916] LOG:  duration: 1157.942 ms  plan:
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