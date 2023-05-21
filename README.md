# Plan2Go
This is the main docker-compose file for Plan2Go.


## run locally
```bash
docker-compose up
```
This will start the Plan2Go application by building a docker-compose network where the microservices 
will be running on. Note that if you haven't pulled the images before, this command will 
automatically pull the images with the tag 'latest' for you. Pull images may take a while.

or to run it in detached mode:
```bash
docker-compose up -d
```
to stop:
```bash
docker-compose down
```
or "Control/Command+C"

### Possible Architecture Mismatch for M1 chip users
For M1 chip users, if you encounter the error 'no matching manifest for linux/arm64 in the manifest list entries', it indicates that Docker is unable to fetch the MongoDB image with the matching architecture for your host machine. In such cases, you can resolve the issue by replacing 'image: mongo' with:
```bash
image: arm64v8/mongo
```
By manually specifying the appropriate image with matching architecture, you can avoid this architecture mismatch problem.

## Pull
For developer colleagues, once you push code to branch "main" or "dev", the images on dockerhub 
will be automatically updated by our Github's CI/CD pipeline. However, the command "docker-compose up" 
will NOT automatically re-pull the images if the images are already downloaded and cached locally.

To re-pull the latest images:
```bash
docker-compose pull
```

