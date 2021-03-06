# Elasticache
- Memory is faster than disk => can speed up database queries by storing frequently accessed data in an in-memory cache
- Is an in-memory cache (key value data store): makes it easy to deploy, operate and scale an in-memory cache in the cloud
- Improve database performance: allow you to retrieve information from fast, in-memory caches instead of slower disk-based storage
- Great for read-heavy database workload: caching result of i/o intensive database queries. Also used for storing session data for distributed apps
- Example: 
	- A busy database serving thousands of queries a day and many of these queries requesting same data => Can use an elasticache cluster to cache frequently accessed data
- 2 types: 
	- Memcached: 
		- Great for basic object caching
		- Scale horizontally, no persistence, no multi az or failover
		- Good choice for basic caching and if you want your caching model to be as simple as possible
	- Redis
		- A more sophisticated solution with enterprise features like persistence, replication, multi-az and failover
		- Support sorting and ranking data (eg: gaming leaderboards) and complex data types like list and hashes
- Typical scenario
	- Database is under a lot of stress
	- Find a solution: which service you should use to alleviate this
	- Elasticache is a good choice if your database is particularly read-heavy and not prone to frequent changing
- When elasticache cannot help:
	- Write-heavy workload => need to scale up your database
	- OLAP queries => think about RedShift (a data warehouse service)
	- 