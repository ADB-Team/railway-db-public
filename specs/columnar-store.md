# Table of Contents

- [Postgres-specific: Implementation](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#postgres-specific-implementation)
- [Columnar Store 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-1)
- [Columnar Store 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-2)
- [Columnar Store 3](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#columnar-store-3)
- [Join Group 1](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-1)
- [Join Group 2](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#join-group-2)
- [Experiment](https://github.com/ADB-Team/railway-db-public/blob/main/specs/columnar-store.md#experiment)

# Postgres-specific: Implementation

Postgres doesn't have a native columnar storage solution → we will use VIEWs and [cstore_fdw by citusdata](https://github.com/citusdata/cstore_fdw)/[zedstore](https://github.com/greenplum-db/postgres/tree/zedstore)

# Columnar Store 1
Make table `routes` a columnar store.

**Used in:**

- [transaction 1](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [transaction 3](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [transaction 5](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [backup transaction 2](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md)
- [backup transaction 5](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)

**Expectations:**

- speed up of transaction 1, 5 and backup transaction 5 because there we read from `routes`
- slow down with transaction 3 because there we write to `routes`
- slow down with backup transaction 2 because we delete `routes`

# Columnar Store 2
Make table `seats` a columnar store.

**Used in:**

- [transaction 1](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [transaction 5](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md)
- [backup transaction 4](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)

**Expectations:**

- speed up transaction 1, 5 and backup transaction 4 because there we read from `seats`

# Columnar Store 3
Columnar store for column `name` of `stations`.

**Used in:**

- [transaction 1](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md)
- [transaction 3](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction3.md)
- [backup transaction 5](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)

**Expectations:**

- speed up transaction 1 because there we filter `stations` by `name`
- slow down transaction 3 because there we write to `stations` so the columnar store needs to be updated as well
- slow down [backup transaction 2](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction2.md) because we delete from `stations` so the columnar store needs to be updated
- backup transaction 5 will probably have the same execution time as before because we also access other columns of `stations`

# Join Group 1
Join group with `seats`, `wagons` and `trains`.

**Used in:**

- [backup transaction 4](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction4.md)

**And used in following function:**
```sql
/*
* Returns a table with all seats from (a) given train connection(s) and if they're occupied.
*/
CREATE OR REPLACE FUNCTION get_seats_from_connection (int)
RETURNS TABLE(seat int, is_taken bool, connection int)
	AS $$
		DECLARE
			s record;
		BEGIN
			FOR s IN (
				SELECT seat_id
				FROM seats
				JOIN wagons ON wagon = wagon_id
				JOIN trains ON wagons.train = train_id
				JOIN schedule ON schedule.train = train_id
				WHERE connection_id = $1
			)
			LOOP
				seat := s.seat_id;
				is_taken := (
					SELECT is_seat_occupied(s.seat_id, $1)
				);
				connection := $1;
				RETURN NEXT;
			END LOOP;
		END;
	$$
	LANGUAGE plpgsql;
```

Which again is **used in transactions:**

- [transaction 1](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction1.md#main-query)
- [transaction 5](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction5.md#calculate-occupancy-rat)

**Expectations:**

- speed up of transaction 1, 5 and backup transaction 4 because they could use this join group

# Join Group 2
Join group with `cities` and `countries`

**Used in:**

- [transaction 7](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/transaction7.md#delete-from-passengers_schedule-for-every-connection-with-this-train)
- [backup transaction 5](https://github.com/ADB-Team/railway-db-public/blob/main/query-plans/original/backup-transaction5.md)

**Expectations:**

- speed up of transaction 7  and backup transaction 5 which could potentially make use of this new join group

# Experiment

Compare performance and usability of two different extensions for columnar store compression:

- [cstore_fdw by citusdata](https://github.com/citusdata/cstore_fdw)
- [zstore](https://github.com/greenplum-db/postgres/tree/zedstore)

Equivalent to comparing compression algorithms as those extensions implement columnar storage in a different way.
