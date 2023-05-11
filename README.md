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

## Test deploying on AWS with LocalStack

NOTICE: using AWS ECS local testing provided by LocalStack requires their "pro" edition.

### 1. Install the offical aws cli provided by aws:
```bash
pip install aws
```
### 2. Install the wrapped cli awslocal provided by localstack
```bash
pip install awscli-local
```
### 3. Start localstack:            
#### *Option1*: use localstack cli

```bash
# for MacOS with brew:
brew install localstack

# or
# with pip:
pip install localstack
```

```bash
# set environment variable to use the pro version
export LOCALSTACK_API_KEY={api_key}
```

```bash
# start localstack
localstack start 
```

#### *Option2*: run localstack in a docker container
```bash
docker run \
  --rm -it \
  -p 4566:4566 \
  -p 4510-4559:4510-4559 \
  localstack/localstack-pro       
```
see the [localstack documents](https://docs.localstack.cloud/getting-started/installation/#docker) for more details. 


### 4. Register the task definition with ECS 
In this folder, there is a well-defined taskdef.json. Register this file with ECS and check all tasks by running the following command:

```bash
# Register
awslocal ecs register-task-definition --cli-input-json file://taskdef.json
```
AWS will return a json-formatted taskDefinishtion back, in which there is a "taskDefinitionArn".

This ARN number is formatted like:
```bash
arn:aws:ecs:<region>:<account-id>:task-definition/<family>:<revision>
```
for example: 
```bash
# copy this ARN numeber
arn:aws:ecs:us-east-1:000000000000:task-definition/plan2go-app:2
```
### 5. Create a new ECS cluster
```bash
# create a new cluster:
awslocal ecs create-cluster --cluster-name new-cluster-name

# check all clusters:
awslocal ecs list-clusters
```

### 5. Run the task
Once we have the task and cluster defined, we can run this task on this cluster by:
```bash
# Run
awslocal ecs run-task --cluster {cluster-name} --task-definition {ID_or_ARN_number_of_the_task}

# Check the task is running
awslocal ecs list-tasks
```


### 5. Push the Docker images to AWS Local ECS by running the following commands:
```bash
docker-compose push
```


### 6. Install the Session Manager plugin, you can follow the instructions in the AWS documentation:

Download the appropriate Session Manager plugin package for your operating system from the [AWS Systems Manager Session Manager Plugin page](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html#install-plugin-macos).

Check if the session-manager-plugin is successfully installed by running:
```bash
session-manager-plugin
```

### 6. Start the Docker Compose stack by running the following command:
```bash
awslocal ecs execute-command --cluster my-cluster --task my-task --container my-container --command "docker-compose up"
```

By now, the app is running on the AWS ECS simulated by localstack.


## Appendix
In case we need to use a bucket, after LocalStack starts:

1. create a new bucket 
```bash
awslocal s3api create-bucket --bucket plan2go-bucket
```

2. we can check the list of buckets in s3 anytime by:
```bash
awslocal s3 ls --endpoint-url=http://localhost:4566
```

3. see what's inside a bucket: 
awslocal s3 ls s3://<bucket-name>/<directory> in this case:
awslocal s3 ls s3://plan2go-bucket