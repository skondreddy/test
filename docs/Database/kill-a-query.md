# Kill a Query
Never kill a linux postgres process with the kill command. It will cause the DB to crash. Instead, use a query to safely kill a query: 


## Login
Make sure you are logged in as the same user that is running the query you want to kill: 
```
user/pass:
sescouser/***** 
sescojobs/***** 
```


## Identify the query to kill: 
```
SELECT pid, age(clock_timestamp(), query_start), usename, query , state 
FROM pg_stat_activity  
WHERE  state != 'idle' 
AND query NOT ILIKE '%pg_stat_activity%'  
ORDER BY query_start desc; 
```


## Kill the Query
Take the PID and run it in the query below 

`SELECT pg_cancel_backend(<pid FROM above you want TO kill>);`


If it is very stubborn and won't die: This kills the entire connection the query is running from, not just the query 

`SELECT pg_terminate_backend(<pid FROM above you want TO kill>);` 
