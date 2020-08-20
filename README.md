![Deploy to Amazon ECS](https://github.com/kkkooosss/React-app-for-multi-docker-elb-ecs-service-capacity_provider/workflows/Deploy%20to%20Amazon%20ECS/badge.svg))

# **Variations of CI/CD pipeline for multi docker container React App**

[React App Fibonacci calculator developed by Stephen Grider](https://github.com/StephenGrider/multi-docker)

For detailes please have a look at 
![Application preview](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs/blob/master/images/Fibonacci_calculator.png)
 
![Arcitecture diagram](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs/blob/master/images/AWS%20Multi-container%20Docker%20Application.png)

Aforementioned React App utilazes external RDS Postgres and ElastiCash for Redis.

## Variation 1

**Prerequisites** 

- User whith programmatic access to AWS.
- Existing AWS ECS Cluster with corresponding service and initialed task-definition.
- Running AWS RDS Postgres inastance.
- Running AWS ElastiCash for Redis.

_*Please note that you could be charged for usage of those services._

Also, following **secrets** should be added to GitHub repositorie settings:
- for AWS configure:
  - ACCESS_KEY_ID
  - SECRET_ACCESS_KEY
  - AWS_REGION
- for Dockerhub login:
  - DOCKER_ID
  - DOCKER_PASSWORD

This CI/CD pipeline uses GitHub Actions to create a new revision of the task-definition and subsiquently updates a corresponding service inside ECS cluster via AWS CLI.

_Note! Test, Build & Push phases were removed from this version of the pipeline. One can obtane them_ [here](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs).

## Variation 2

**Prerequisites** 

- User whith programmatic access to AWS.
- Elastic Beanstalk with created Application and Environment for Multi container Docker App.
- S3 for keeping zip files for Elastic Beanstalk.
- Dockerhub account for images.
- Running AWS RDS Postgres inastance.
- Running AWS ElastiCash for Redis.

_*Please note that you could be charged for usage of those services._

Also, following **secrets** should be added to GitHub repositorie settings:
- for AWS configure:
  - ACCESS_KEY_ID
  - SECRET_ACCESS_KEY
  - AWS_REGION
- for Dockerhub login:
  - DOCKER_ID
  - DOCKER_PASSWORD
- Postgres DB credentials
  - POSTGRES_USER
  - POSTGRES_PASSWORD

This **Variation 2** of CI/CD pipeline uses GitHub Actions to create a new version of AWS Elastic Beanstalk Multi-Docker Environment and subsiquently deploy it. 

[This is the source code of Variation 2](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs).

[The **Variation 2** via AWS CLI created on Bitbacket-pipeline ](https://bitbucket.org/ConstConst/react-app-for-multi-docker-ci-cd-deployment-to/src/master/).

[*The **Variation 2** based on Travis-CI could be found here (original code by Stephen Grider)](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-elasticbeanstalk-aws-with-travis).
