### Running PostgreSQL in a container

##### Connecting to the database
```bash
podman exec -it greenlight-psql psql -U postgres

podman exec -it greenlight-psql psql -U greenlight

# DSN for connecting from the app
postgres://greenlight:pasas456@127.0.0.1/greenlight?sslmode=disable
```

##### Useful commands
```sql
-- list databases
\l
-- connect to a specific database
\c my-database
-- list tables
\dt
-- list users
\du
-- list commands
\?
```

##### Setting up the container
```bash
podman volume create greenlight-psql-data
podman run --name greenlight-psql --network=host --volume greenlight-psql-data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=pasas123 -d docker.io/library/postgres:17.4-bookworm
```

##### Basic setup
```sql
CREATE DATABASE greenlight;

\c greenlight

CREATE ROLE greenlight WITH LOGIN PASSWORD 'pasas456';

CREATE EXTENSION IF NOT EXISTS citext;

ALTER DATABASE greenlight OWNER TO greenlight;
```
