# Indexes on:

- countries.name
- stations.name
- passengers.surname
- schedule.start_time
- stations.date_created

# Examples for usage

## countries.name

- in transaction 7:

```
Seq Scan on countries  (cost=0.00..4.79 rows=1 width=10)
	Filter: ((name)::text = 'Germany'::text)
```

- in backup transaction 2:

```
Seq Scan on countries  (cost=0.00..4.79 rows=1 width=6)
	Filter: ((name)::text = 'Poland'::text)
```

- in backup transaction 5:

```
Seq Scan on countries countries_1  (cost=0.00..4.79 rows=1 width=4)
	Filter: ((name)::text = 'United States'::text)
```

## stations.name

- in transaction 3:

```
Seq Scan on stations stations_1  (cost=0.00..402.59 rows=99 width=4)
	Filter: ((name)::text = 'Berlin Görlitzer Bhf'::text)
```

- in transaction 5:

```
Seq Scan on stations  (cost=0.00..402.59 rows=99 width=4)
	Filter: ((name)::text = 'Weinsberg East'::text)
```

## passengers.surname

- in transaction 1:

```
Seq Scan on passengers  (cost=0.00..571.00 rows=49 width=4)
	Filter: ((surname)::text = 'Cooper'::text)
```

## schedule.start_time

- in transaction 1:

```
Seq Scan on schedule  (cost=1223.62..1767.59 rows=1 width=4)
	  Filter: ((route = $2) AND (start_time = $3) AND ((train)::text = ($4)::text))
```

## stations.date_created

- in backup transaction 5:

```
Seq Scan on stations  (cost=0.00..551.14 rows=5466 width=32)
	Filter: ((nr_platforms > 5) AND (date_part('year'::text, (date_created)::timestamp without time zone) > '1950'::double precision))
```

# Types

- b-tree works always but hash index should be sufficient for the name colums
- date_created would probably be better with b-tree, comparison is slightly more complex than =

# Experiments

- compare runtimes when indexes are hash index vs. indexes are b-tree indexes
