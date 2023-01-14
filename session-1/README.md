##### running containers
`docker run alpine hostname`

##### print running containers:
`docker container ls`

##### or use ps
`docker ps `

##### the -a flag shows all statuses
`docker ps -a`

##### run the container
`docker run -it kiamol/ch15-cert-generator`
##### ctrl+c to exit

##### find the container ID and print the log
`docker container ls -a `
`docker container logs <container-id>`

##### running interactive container
`docker run -it alpine`

##### explore the contaier
```
ls /
whoami
hostname
cat /etc/os-release
exit
```

##### run other container
`docker run -it ubuntu`

##### try curl any website
```
curl https://github.com 
apt install -y curl
apt update
apt install -y curl
curl https://github.com
exit
```

##### run other ubuntu container
`docker run ubuntu bash -c 'curl https://github.com'`

##### impress people with your hacking skills
`docker run -t sixeyed/hollywood`
##### ctrl+c to exit

##### running a background container
`docker run -d -P --name nginx1 nginx:alpine`

##### -d flags to run container in background
##### -P publish network ports to, we can access through this port
##### --name set name for container

##### print container port mapping
`docker container port nginx1`

##### browse through browser or curl 
`curl localhost:<container-port>`

> Working env variables
##### print envrionment values
`docker run alpine printenv`
`docker run openjdk:8-jre-alpine printenv`

##### set our own environment variables
`docker run -e Course=Container alpine printenv`
##### env can't be edited, they're set lifetime container

##### override default variables container
`docker run -e Course=Container -e LANG=C.UTF-16 openjdk:8-jre-alpine printenv`

##### create env file
```
cat <<EOF > exercise.env
Course=Container
LOG_LEVEL=debug
FEATURES_CACHE_ENABLED=true
EOF
```

##### set env variables by file
`docker run --env-file exercise.env alpine printenv`

> container filesystem
##### run container in background
`docker run -d --name nginx nginx:alpine`

##### exec to container
```
docker exec -it nginx sh
ls /usr/share/nginx/html/
cat /usr/share/nginx/html/index.html
exit
```

##### create index.html inside html directory
`mkdir html`
```
cat <<EOF > html/index.html
<h1> Hello from StudiDevOps </h1>
EOF
```

##### export index.html path
`htmlFolder="$(pwd)/html"`

##### powershell
`$htmlFolder="${pwd}/html"`

##### mount local folder to container
`docker run -d -p 8081:80 -v ${htmlFolder}:/usr/share/nginx/html --name nginx2 nginx:alpine`
##### -v mounts a local directory to the container

##### access the page
`curl http://localhost:8081`

> Compute resources
##### run container without restriction
`docker run -d -p 8083:80 --name pi kiamol/ch05-pi -m web`

##### access the page
`http://localhost:8031/pi?dp=50000`
##### how much it takes?

##### inspect the container
`docker inspect pi | grep 'NanoCpus\|Memory'`

##### run the container with limitation
`docker rm -f pi`
`docker run -d -p 8031:80 --name pi --memory 200m --cpus 0.25 kiamol/ch05-pi -m web`

##### access the page again
`http://localhost:8031/pi?dp=50000`
##### how much it takes?
##### little bit longer 

##### inspect the container again
`docker inspect pi | grep 'NanoCpus\|Memory'`

> TASK
##### COPY FILES FROM CONTAINER TO LOCAL
##### copy file server-cert.pem and server-key.pem from /certs 
`docker run -d --name tls kiamol/ch15-cert-generator`


##### clean up
`docker rm -f $(docker ps -aq)`