```sql
-- создать вью для просмотра активных локов
CREATE OR REPLACE VIEW public.active_locks AS
SELECT t.schemaname,
t.relname,
l.locktype,
l.page,
l.virtualtransaction,
l.pid,
l.mode,
l.granted
FROM pg_locks l
JOIN pg_stat_all_tables t ON l.relation = t.relid
WHERE t.schemaname <> 'pg_toast'::name AND t.schemaname <> 'pg_catalog'::name
ORDER BY t.schemaname, t.relname;


-- посмотреть активные локи
SELECT * FROM active_locks;

-- посмотреть активные транзакции
SELECT now()-xact_start as xact_runtime,
pid,
usename,
application_name,
state,
query
FROM pg_stat_activity
WHERE xact_start IS NOT NULL
AND pid != pg_backend_pid()
AND backend_type = 'client backend'
ORDER BY xact_runtime DESC;

-- удалить процесс (пид)
SELECT pg_terminate_backend(19212);
```