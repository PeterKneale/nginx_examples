# DockerProxy



## Proxying a subset of paths to another server
```sh
docker network create public
docker network create private

# build and run server
docker build -t server -f server/Dockerfile ./server
docker run --rm -p 8081:80  --network private --name server server

# build and run proxy
docker build -t proxy -f proxy/Dockerfile ./proxy
docker run --rm -p 8080:80 --network public --network private --name proxy proxy
```

### Generating a http basic username and password
```sh
sudo apt install apache2-utils
htpasswd -c proxy/.htpasswd user@example.com
```

## Demo
```sh
curl http://localhost:8080                      # Hits the proxy and serves up the default index file on the proxy
curl http://localhost:8080/proxy.html           # Hits the proxy and serves a file found on the local file system
curl http://localhost:8080/does_not_exist.html  # Hits the proxy returns 404 because its not found on the local file system
curl http://localhost:8080/public.html          # Hits the proxy and is forwarded to the server
curl http://localhost:8080/private.html         # Hits the proxy and not found on the local file system so returns 404
curl http://localhost:8080/secure.html          # Hits the proxy, requests basic auth per `.htpassed` and is forwarded to the server
curl -u user@example.com:password http://localhost:8080/secure.html # this time supply credentials
```