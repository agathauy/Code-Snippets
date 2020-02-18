Postgres dump and undump of a database
```
pg_dump -U username dbname > dbexport.pgsql

psql -U username dbname < dbexport.pgsql
```