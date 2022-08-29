
## Replication

Documentation [here](https://redis.io/topics/replication)

### Configuration

```
#persistence
dir /data
dbfilename dump.rdb
appendonly yes
appendfilename "appendonly.aof"

```
### redis-1 Configuration

```
protected-mode no
port 6379

#authentication
masterauth a-very-complex-password-here
requirepass a-very-complex-password-here
```
### redis-2 Configuration

```
protected-mode no
port 6379
slaveof redis-1 6379

#authentication
masterauth a-very-complex-password-here
requirepass a-very-complex-password-here

```
### redis-3 Configuration

```
protected-mode no
port 6379
slaveof redis-1 6379

#authentication
masterauth a-very-complex-password-here
requirepass a-very-complex-password-here

```

```

# remember to update above in configs!

docker compose up -d
```

## Example Application

Run example application , to show application writing to the master(to run with docker compose)

```
cd ./client/
docker build . -t redis-simple-client
```

## Test Replication

Technically written data should now be on the replicas

```
# go to one of the clients
docker exec -it redis-3 sh
redis-cli
auth "a-very-complex-password-here"
keys *

```

## Running Sentinels

Documentation [here](https://redis.io/topics/sentinel)

```
#********BASIC CONFIG************************************
port 5000
sentinel resolve-hostnames yes
sentinel monitor mymaster redis-1 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1
sentinel auth-pass mymaster a-very-complex-password-here
#********************************************
```
Starting Redis in sentinel mode (docker compose run it)

```
docker logs sentinel-1
docker exec -it sentinel-1 sh
redis-cli -p 5000
info
sentinel master mymaster

# clean up 

docker rm -f redis-1 redis-2 redis-3
docker rm -f sentinel-1 sentinel-2 sentinel-3
```
