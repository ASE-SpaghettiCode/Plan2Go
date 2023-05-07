# Plan2Go
This is the main docker-compose file for Plan2Go.


## run locally
```bash
docker-compose up
```
This will start the Plan2Go application by building a docker-compose network where the microservices 
will be running on. Note that if you haven't pulled the images before, this command will 
automatically pull the images with the tag 'latest' for you. Pull images may take a while.

to stop:

"Control/Command+C"

or you can run it in detached mode:
```bash
docker-compose up -d
```
to stop:
```bash
docker-compose down
```

## Pull
For developer colleagues, once you push code to branch "main" or "dev", the images on dockerhub 
will be automatically updated by our Github's CI/CD pipeline. However, the command "docker-compose up" 
will NOT automatically re-pull the images if the images are already downloaded and cached locally.

To re-pull the latest images:
```bash
docker-compose pull
```