                        
# Cross joining passengers with connection and their seat, for every passenger:

    Query Text: SELECT 1 FROM ONLY "public"."seats" x WHERE "seat_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.42..8.45 rows=1 width=10) (actual time=0.056..0.057 rows=1 loops=1)
	  ->  Index Scan using seats_pkey on seats x  (cost=0.42..8.44 rows=1 width=10) (actual time=0.051..0.051 rows=1 loops=1)
	        Index Cond: (seat_id = 371426)
	        
	        
	        
    Query Text: SELECT 1 FROM ONLY "public"."schedule_unpartitioned" x WHERE "connection_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10) (actual time=0.012..0.012 rows=1 loops=1)
	  ->  Index Scan using schedule_pkey on schedule_unpartitioned x  (cost=0.29..8.30 rows=1 width=10) (actual time=0.010..0.010 rows=1 loops=1)
	        Index Cond: (connection_id = 1)
	        
	        
	        
    Query Text: SELECT 1 FROM ONLY "public"."passengers" x WHERE "passenger_id" OPERATOR(pg_catalog.=) $1 FOR KEY SHARE OF x
	LockRows  (cost=0.29..8.31 rows=1 width=10) (actual time=0.015..0.016 rows=1 loops=1)
	  ->  Index Scan using passengers_pkey on passengers x  (cost=0.29..8.30 rows=1 width=10) (actual time=0.010..0.010 rows=1 loops=1)
	        Index Cond: (passenger_id = 208)
	        
    
