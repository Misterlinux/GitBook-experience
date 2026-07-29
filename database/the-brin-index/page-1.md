# Page 1

* 1
* 1
* 1
* 1
* 1

1

The PostgreSQL extensions are supplemental SQL objects included with the installation files. They introduce new functions, data types, and operations without modifying the core database engine.

The pg\_stat\_statements extension has a minimal memory footprint. It isn't enabled by default and it must be explicitly configured in the postgresql.conf file.

1

```sql

-- We need a server restart to include it in a running server
C:\Program Files\PostgreSQL\14\data\postgresql.conf
#session_preload_libraries = ''
shared_preload_libraries = 'pg_stat_statements'		# (change requires restart)
pg_stat_statements.max = 10000
```

1

1

1

1

1
