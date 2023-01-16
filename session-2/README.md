# Building Container Images

## Working with images
```
docker image --help

docker build --help

docker history --help
```

### Base Image
Configure Docker to use original build engine
```
# on macOS or Linux:
`export DOCKER_BUILDKIT=0`
# OR with PowerShell:
`$env:DOCKER_BUILDKIT=0`
```

 1. Create folder in current directory
`mkdir base`
 2. Create Dockerfile
	```
	cat <<EOF > base/dockerfile
	FROM ubuntu
	EOF
	```

3. Create image
	 `docker build -t studidevsecops/base base` 
	 > **-t** or **--tag** give the image name

 4.  List all images
	 `docker images --all`

 5. Pull images
	`docker pull ubuntu`

6. Pull with specific tag
	`docker pull ubuntu:22.04`

7.	List all ubuntu images
	`docker image ls --filter reference=ubuntu --filter reference=studidevsecops/base`
> See the IMAGE ID

  

## Command and entrypoint

Dockerfile sytanx easy to learn
**RUN** to execute command
**CMD** set the command to run, when container starts

1. Create a folder
`mkdir curl`

2. Create a dockerfile
	```
	cat <<EOF > curl/Dockerfile
	FROM alpine
	RUN apk add curl
	CMD curl
	EOF
	```
3. Build Image
	`docker build -t yourname/curl curl/`

4. Run the container
	`docker run yourname/curl`

5. Run the container and pass the url
	`docker run yourname/curl https://studidevsecops.com`

6. Use curl command
	`docker run yourname/curl curl --head https://studidevsecops.com`

7. Update our image, by creating new Dockerfile
	```
	cat <<EOF > curl/Dockerfile.v2
	FROM alpine:3.16  

	RUN apk add --no-cache curl=7.83.1-r5

	ENTRYPOINT ["curl"]
	EOF
	```
 8. Update image
	`docker build -t studidevsecops/curl:v2 -f curl/Dockerfile.v2 curl`

 9. List new images
	`docker images --filter reference=studidevsecops/curl`

	> New size image smaller

 
## Image hierrarchy

1. Create Folder
	`mkdir web`

2. Create dockerfile
	```
	cat <<EOF > web/Dockerfile
	FROM nginx:1.23.0-alpine

	COPY index.html /usr/share/nginx/html/
	EOF
	```

3. Create index.html
	```
	cat <<EOF > web/index.html
	<h1> Hello World!</h1>

	<h6> Made by yourname </h6>
	EOF

	```

4. Build image
	`docker build -t studidevsecops/web web`

5. Run the container
	`docker run -d -p 8090:80 studidevsecops/web` 

## Accessing Images on Registries
> To work with registries, we still use images

	```
	docker image --help

	docker pull --help

	docker push --help

	docker tag --help
	```

## Push images

1. Export our dockerid
    ```
	on Linux or macOS:
	`export dockerId='yourusername'`

	OR with PowerShell:
	`$dockerId='yourusername'`
	```
2. Build image
	`docker build -t ${dockerId}/curl:21.06 -f curl/Dockerfile.v2 curl`

3. Verify the images
	`docker images --filter reference=yourusername/curl`
4. Login to registries
	`docker login -u ${dockerId}`

5. Push the images
	`docker push ${dockerId}/curl:21.06`

6. Docker hub images are public repositories, browse the url
	`echo "https://hub.docker.com/r/${dockerId}/curl/tags"`

  

## Tags and references

> Image name (references) built from 3 parts
	**domain** of the container registry
	**repository name** - identifier the app, the owner or organization
	**tag** - can be anything, usually for versioning

1. Create new reference from image using `tag` command
	`docker tag ${dockerId}/curl:21.06 ${dockerId}/curl:21.07`
2. Verify the images
	`docker images --all | grep ${dockerId}`
3. Push individual tags
	`docker push ${dockerId}/curl:21.07`
4. Push all local tag
	`docker push --all-tags ${dockerId}/curl

## Running local registry

1. Creating local registry
	`docker run -d -p 5000:5000 --name registry registry:2.8.1`
2. Verify the lgos
	`docker logs registry`
> domain local registry is localhost:5000

> full image reference
   [registry-domain]/[repository-name]:[tag]

> public registry
   [registry-domain]/[user-id]/[image-id]:[tag]  

3. Tag image new image
	`docker tag alpine localhost:5000/alpine`

4. Push images to local repository
	`docker push localhost:5000/alpine`
	> If facing issue reference this https://docs.docker.com/registry/insecure/deploy-a-plain-http-registry	 			local registry doesn't have UI but it works with REST API

	`curl --head localhost:5000/v2/`
	`curl localhost:5000/v2/alpine/tags/list`

5. remove image from the registry
	`docker image rm localhost:5000/alpine`

6. Create and push more tags for the alpine image
	`docker tag alpine localhost:5000/alpine:21.07`
	`docker tag alpine localhost:5000/alpine:local`

7. push all tag images
	`docker push --all-tags localhost:5000/alpine`

 8. verify the images
	`curl localhost:5000/v2/alpine/tags/list`

## TASK
1. Build an image (any image)
2. Push to private registry example ECR
3. Run the image

Screenshoot the Dockerfile, your private registry and output of your image