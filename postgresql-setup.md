### Running PostgreSQL in a container

##### Connecting to the database
```bash
podman exec -it greenlight-psql psql -U postgres

podman exec -it greenlight-psql psql -U greenlight

# DSN for connecting from the app
postgres://greenlight:pasas456@127.0.0.1/greenlight?sslmode=disable
# For convenience add DSN env to bashrc
echo 'export GREENLIGHT_DB_DSN=postgres://greenlight:pasas456@127.0.0.1/greenlight?sslmode=disable' > ~/.bashrc
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

##### Migrations
```bash
# migrate tool can be downloaded at https://github.com/golang-migrate/migrate/releases

# Running migrations
migrate -path=./migrations -database=$GREENLIGHT_DB_DSN up
```

##### Test data
```bash
BODY='{"title":"Moana","year":2016,"runtime":"107 mins","genres":["animation","adventure"]}'
curl -i -d "$BODY" localhost:4000/v1/movies

BODY='{"title":"Black Panther","year":2016,"runtime":"134 mins","genres":["sci-fi","action","adventure"]}'
curl -i -d "$BODY" localhost:4000/v1/movies
curl -i -XPATCH -d '{"year":2018}' localhost:4000/v1/movies/2

BODY='{"title":"Black Panther 2","year":2020,"runtime":"130 mins","genres":["sci-fi","action","adventure"]}'
curl -i -d "$BODY" localhost:4000/v1/movies
curl -i -XDELETE localhost:4000/v1/movies/3

BODY='{"title":"The Breakfast club","year":1986,"runtime":"95 mins","genres":["comedy","action"]}'
curl -i -d "$BODY" localhost:4000/v1/movies
curl -i -XPATCH -d '{"title":"The Breakfast Club"}' localhost:4000/v1/movies/4
curl -i -XPATCH -d '{"runtime":"97 mins"}' localhost:4000/v1/movies/4
curl -i -XPATCH -d '{"year":1985}' localhost:4000/v1/movies/4
curl -i -XPATCH -d '{"genres":["comedy"]}' localhost:4000/v1/movies/4

curl -i localhost:4000/v1/movies
```

##### Creating API users
```bash
BODY='{"name": "Alice Smith", "email": "alice@example.com", "password": "pasas789"}'
curl -i -d "$BODY" localhost:4000/v1/users
```
