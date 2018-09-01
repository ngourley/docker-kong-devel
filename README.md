# docker-kong-devel

### Run

#### Set Image Name

##### Windows
```batch
set IMAGE_NAME=kong-local
```

##### Linux
```sh
export IMAGE_NAME=kong-local
```

#### Build Image
```batch
docker build -t %IMAGE_NAME% .
```

#### Run macOS or Linux
```sh
docker build -t $IMAGE_NAME .
```


#### Start Containers

##### Windows
```batch
docker rm -f kong-database

docker run --rm ^
-d ^
-e POSTGRES_USER=kong -e POSTGRES_DB=kong ^
--name=kong-database postgres:9.6

docker run --rm ^
-d ^
--link kong-database:kong-database ^
-e "KONG_DATABASE=postgres" ^
-e "KONG_PG_HOST=kong-database" ^
%IMAGE_NAME%:latest kong migrations up

docker rm -f kong
docker run -p 8000:8000 -p 8443:8443 -p 8001:8001 ^
-e KONG_DATABASE=postgres ^
-e KONG_PG_HOST=kong-database ^
-v %cd%:/opt/rh/httpd24/root/var/www/html ^
-v %cd%/plugins/kong/plugins/ldap-auth:/usr/local/share/lua/5.1/kong/plugins/ldap-auth ^
--link kong-database:kong-database ^
--name=kong ^
-d ^
%IMAGE_NAME%
```

##### Linux
```sh
docker rm -f kong-database
docker run \
-d \
-e POSTGRES_USER=kong -e POSTGRES_DB=kong \
--name=kong-database postgres:9.6

docker run --rm \
-d \
--link kong-database:kong-database \
-e "KONG_DATABASE=postgres" \
-e "KONG_PG_HOST=kong-database" \
$IMAGE_NAME:latest kong migrations up

docker rm -f kong
docker run -p 8000:8000 -p 8443:8443 -p 8001:8001 \
-e KONG_DATABASE=postgres \
-e KONG_PG_HOST=kong-database \
-v $(pwd):/opt/rh/httpd24/root/var/www/html \
-v $(pwd)/plugins/kong/plugins/ldap-auth:/usr/local/share/lua/5.1/kong/plugins/ldap-auth \
--link kong-database:kong-database \
--name=kong \
-d \
$IMAGE_NAME
```

#### Attach to Container
```sh
docker exec -it kong bash
```

#### Load Changes, Follow Logs
```sh
kong reload && tail -f /usr/local/kong/logs/error.log
```

