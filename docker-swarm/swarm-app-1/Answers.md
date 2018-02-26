# Create networks - frontend and backend
docker network create frontend --driver overlay
docker network create backend --driver overlay

# Create Volume for Postgres DB
docker volume create db-data

# Serice Name : vote
docker service create --name vote --replicas 2 --network frontend -p 80:80 dockersamples/examplevotingapp_vote:before

# Service Name : redis
docker service create --name redis --replicas 1 --network frontend redis:3.2

# Service Name : worker
docker service create --name worker --replicas 1 --network frontend --network backend dockersamples/examplevotingapp_worker

# Service Name : db
docker service create --name db --replicas 1 --network backend --mount type=volume,source=db-data,target=/var/lib/postgresql/data -e POSTGRES_PASSWORD=admin postgres:9.4

# Service Name : result
docker service create --name result --replicas 1 --network backend -p 5001:80 dockersamples/examplevotingapp_result:before