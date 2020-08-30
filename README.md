![Deploy to Amazon ECS](https://github.com/kkkooosss/React-app-for-multi-docker-elb-ecs-service-capacity_provider/workflows/Deploy%20to%20Amazon%20ECS/badge.svg)


# **Variations of CI/CD pipeline for multi docker container React App**

[React App Fibonacci calculator developed by Stephen Grider](https://github.com/StephenGrider/multi-docker)

For detailes please have a look at 

## Application preview
![Application preview](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs/blob/master/images/Fibonacci_calculator.png)

## Arcitecture diagram 
![Arcitecture diagram](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs/blob/master/images/AWS%20Multi-container%20Docker%20Application.png)

Aforementioned React App utilazes external RDS Postgres and ElastiCash for Redis.

## Variation 1

**Prerequisites** 

- User whith programmatic access to AWS.
- Existing AWS ECS Cluster with corresponding service and initiated task-definition.
- Running AWS RDS Postgres instance.
- Running AWS ElastiCash for Redis.

_*Please note that you could be charged for usage of those services._

Following **secrets** should exist in GitHub repository settings:
- for AWS configure
  - ACCESS_KEY_ID
  - SECRET_ACCESS_KEY
  - AWS_REGION
- for Dockerhub login
  - DOCKER_ID
  - DOCKER_PASSWORD

This CI/CD pipeline uses GitHub Actions to do tow jobs.
- Test, build, and update docker images of React App Fibonacci calculator, and push them to ECR. 
- Create a new revision of the task-definition and subsequently update a corresponding service inside ECS cluster via AWS CLI.


## Variation 2

**Prerequisites** 

- User whith programmatic access to AWS.
- Elastic Beanstalk with created Application and Environment for Multi container Docker App.
- S3 for keeping zip files for Elastic Beanstalk.
- Dockerhub account for images.
- Running AWS RDS Postgres instance.
- Running AWS ElastiCash for Redis.

_*Please note that you could be charged for usage of those services._

Following **secrets** should exist in to GitHub repository settings:
- for AWS configure
  - ACCESS_KEY_ID
  - SECRET_ACCESS_KEY
  - AWS_REGION
- for Dockerhub login
  - DOCKER_ID
  - DOCKER_PASSWORD
- RDS Postgres credentials
  - POSTGRES_USER
  - POSTGRES_PASSWORD

This **Variation 2** of CI/CD pipeline uses GitHub Actions to create a new version of AWS Elastic Beanstalk Multi-Docker Environment and subsiquent deploy. 

[This is the source code of Variation 2](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs).

[The **Variation 2** via AWS CLI created on Bitbacket-pipeline ](https://bitbucket.org/ConstConst/react-app-for-multi-docker-ci-cd-deployment-to/src/master/).

[*The **Variation 2** based on Travis-CI could be found here (original code by Stephen Grider)](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-elasticbeanstalk-aws-with-travis).

___
___

## Environment creation process pipeline workflow.

_Note! All resources should be created in the same VPC !_
___
**Create Security group**
- "db-redis-sg".
- Inbound  :  6379 (Redis) , 5432
(PostgresDB)
- Outbound : all
- Source   : id-********* of db-redis-sg (copy/paste it to source)

_Note! You could not do it by the first step as this Security group not exist yet and there is no such source. So choose source 0.0.0.0/0 during creating and make a change after that._


**Create a Security group** 
-"http-sg".
- Inbound  : 80 (http)
- Outbound : all
- Source   : 0.0.0.0/0
___

**Create execution role** "sandbox-exec-role"
- Permissions policy: AmazonECSTaskExecutionRolePolicy 

Update ARN of "executionRoleArn" in the task-definition.json file with your new "sandbox-exec-role" Role ARN. GitHub Action will start the pipeline after your commitment. Deployment job will stop with ERROR as we have no environment yet.
It's Okay.
___
**Create Application Load Balancer**  "elb-multi-docker" with internet-facing.
___
Choose all subnets you going to use for ECS cluster (one at least).
Attache "http-sg" to ALB.
Add "elb-tg" as "Target group"
___
**Create RDS Postgres DB instance.** Use AWS CLI for it.

```
aws rds create-db-instance \
    --db-instance-identifier multi-docker \
    --db-instance-class db.t2.micro \
    --engine postgres \
    --no-publicly-accessible \
    --master-username postgres \
    --master-user-password hardsuperpass-word \
    --db-security-groups db-redis-sg \
    --no-multi-az \
    --allocated-storage 20 
```
_Note! If command stuck with error "API versions for DB Security group" remove "--db-security-groups db-redis-sg" from command.
Attache the Security group "db-redis-sg" to DB instance by the Consol and remove the default security group accordingly._
    
EntryPoint you can get by cli command or find it in RDS Console. Add it to Secrets to your GitHub repository settings as "POSTGRESS_HOST"  
```
aws rds describe-db-instances --query DBInstances[].Endpoint[].Address[] | grep multi-docker*   
```
___
**Create ElasiCash cluster for Redis.**
```
aws elasticache create-cache-cluster \
    --cache-cluster-id "new-cluster-Redis" \
    --engine redis \
    --cache-node-type cache.t2.micro \
    --num-cache-nodes 1 \
    --az-mode single-az
    --security-group-ids 'put here ID of "db-redis-sg"'
```    
EntryPoint you can get by cli command or find it in ElastiCash Console. Add it to Secrets to your GitHub repository settings as "REDIS_HOST".
```     
aws elasticache describe-cache-clusters --show-cache-node-info --query CacheClusters[].CacheNodes[].Endpoint[].Address | grep multi-docker*
```
___
**Create ECR repositories**
- multi-client
- multi-nginx
- multi-server
- multi-worker
___

**Create ECS Cluster**
Create a new ECS cluster with the following parameters.

- Cluster type                 : EC2 Linux + Networking
- Cluster name                 : multi-docker
- EC2 instance type            : t2.micro (good enough for our application and free if you use AWS free tier)
- Number of instances          : 2 (you can add more, but not less then 2)
- VPC                          : default vpc for your region and select any AZ or few AZs
- Security group               : db-redis-sg (Security group we created earlier)
- others by default

After creating cluster go to Console of EC2. Find EC2 instances in cluster **multi-docker** (named "ECS Instance-EC2ContainerService-multi-docker") and attache second Security group (http-sg) to it.

_Note! We should have tow Security groups attached to EC2 inside our cluster._

Open Target group tab and add both EC2 instances to target group "elb-tg".
___
Go back to ECS Consol. 
**Create a new Task-Definition** with the following parameters.
- Lunch type                : EC2
- Task Definition Name      : multi-docker-task-definition
- Network Mode              : bridge (it is by default)
- Task execution role       : sandbox-exec-role
- others by default


Scroll down to the end and push "Configure via JSON"
Copy/paste content of task-definition.json file from this repository.

Say "Save" and "Create".

You have to see a new revision of "multi-docker-task-definition" with four containers.
___
**Create a service.**
Go to our cluster -> Clusters/multi-docker
Create service with the following parameters.

- Launch type           : EC2
- Task Definition       : multi-docker-task-definition
- Cluster               : multi-docker
- Service name          : multi-docker-service
- Number of tasks       : 1 (you can choose more, depend on EC2 type you choose)
- others by default

- Say "Next step"
- Select our Application Load Balancer "elb-multi-docker"

  - Container name    : port 80:HTTP  Say "Add to load balancer"
  - Target group name : elb-tg

- Say "Next step"
- You cand adjust "Set Auto Scaling policy" or keep it by default (NOT adjusted)
- Check the Review.

 _Note! Please double check that you keep 
Minimum healthy percent : 100
Maximum percent         : 200
It will let us dropdown-zero update of our application_
- Say "Create service"
___
You should see the PENDING status of our task. It's Okay.

The final step - you have to add your credentials to Secrets in the "Setting" tab in your GitHub repository.
___
**Total 9 secrets.**

for AWS configure
- ACCESS_KEY_ID
- SECRET_ACCESS_KEY
- AWS_REGION

RDS Postgres and Redis Endpoints&credentials
- POSTGRES_USER
- POSTGRES_PASSWORD
- POSTGRESS_HOST (This one we added erlier)
- REDIS_HOST (This one we added erlier)
- PGDATABASE

for ECR login
- ECR_PREF ( URL of your ECR  "your_account.dkr.ecr.eu-central-1.amazonaws.com". You can find it to select  "View push commands" in your ECR.)
___
Now you can use the pipeline which will be update React App after each push action to master branch.

GitHub Actions will start the pipeline after your commitment. It takes a few minutes. You should see the RUNNING status of task and all containers inside of it.
Now your pipeline is ready and working.

**Congratulations!!!**
___
Check it.

As an example, edit file "React-app-for-multi-docker-elb-ecs-service-capacity_provider/client/src/App.js"

Change "This is a Fibonacci Calculator" to "Fantastic Fibonacci Calculator"

Say "Commit"
___
On the Actions page in the repository, you can see a workflow process.
After succeed in the deployment step check the latest revision of "multi-docker-task-definition". It will be updated to a new one.
___
After that "multi-docker-service" will update the revision of "multi-docker-task-definition"
See the result by updating the browser.
_Note! The processes take a while time to finish. And we have to reload the page in the browser each time we put in the index to the calculator. Don't ask me why..._
___
ATTENTION!!! Remove ALL created resources after the finish. Otherwise, you could be charged for them.
Double-check that you removed:

- Cluster "multi-docker" 
- RDS Postgres DB instance
- ElastiCash for Redis instance
- Deregister task definition  "multi-docker-task-definition"
- Load balancer "elb-multi-docker"
- Security groups "db-redis-sg","http-sg"
- ECR "multi-docker" with all images.


Good luck!
