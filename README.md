# Jakarta Data Quickstart for WildFly 35

The Jakarta Data Quickstart for WildFly 35 provides an initial demonstration of
how to integrate Jakarta Data into a Jakarta EE 10 or 11 application that runs
on WildFly version 35.

This quickstart builds upon the existing WildFly [todo-backend](https://github.com/wildfly/quickstart/tree/main/todo-backend)
quickstart by replacing Jakarta Persistence with Jakarta Data. 

The quickstart was initially created by [Brian Stansberry](https://github.com/bstansberry).
You can find his original code [here](https://github.com/bstansberry/quickstart/tree/jakarta_data/todo-jakarta-data).

You can read more about Jakarta Data support in WildFly in [this blog post](https://www.wildfly.org/news/2024/10/10/jakarta-data/).

## Building, running and testing locally

You need Maven, Java 17 JDK and Docker to build and run the application locally.

1. Package the backend:
```sh
mvn clean package -Pprovisioned-server
```
2. Run a local PostgreSQL database with Docker:
```sh
docker run --rm --name todo-backend-db \
    -e POSTGRES_USER=todos \
    -e POSTGRES_PASSWORD=mysecretpassword \
    -p 5432:5432 postgres
```
3. Run the application, passing database connection parameters in environment variables:
```sh
./target/server/bin/standalone.sh \
    -Denv.POSTGRESQL_DATABASE=todos \
    -Denv.POSTGRESQL_DATASOURCE=ToDos \
    -Denv.POSTGRESQL_SERVICE_HOST=localhost \
    -Denv.POSTGRESQL_SERVICE_PORT=5432 \
    -Denv.POSTGRESQL_USER=todos \
    -Denv.POSTGRESQL_PASSWORD=mysecretpassword
```
4. Run the integration test that uses the HTTP client against the local server HTTP API:
```sh
mvn verify -Pintegration-testing -Dserver.host=http://localhost:8080 
```
5. Use curl and the local server HTTP API to test managing the list of todos:
```sh
# Get a list of todos
$ curl http://localhost:8080
[]

# Create a todo with the title "This is my first todo item!"
$ curl \
    -X POST \
    -H "Content-Type: application/json"  \
    -d '{"title": "This is my first todo item!"}' \
    http://localhost:8080
{"completed":false,"id":1,"order":0,"title":"This is my first todo item!","url":"https://localhost:8080/1"}%

# Get a list of todos with the one that was just created
$ curl http://localhost:8080
[{"completed":false,"id":1,"order":0,"title":"This is my first todo item!","url":"https://localhost:8080/1"}]
```
