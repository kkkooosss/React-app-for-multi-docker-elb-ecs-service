![Deploy to Amazon ECS with ecs-cli & Elastic Beanstalk](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs/workflows/Deploy%20to%20Amazon%20ECS%20with%20ecs-cli%20&%20Elastic%20Beanstalk/badge.svg)

# **React-app-for-multi-docker-elb-ecs-service-capacity_provider**

This CI/CD pipeline uses GitHub Actions which makes a deploymeny of new revision of sample multi container Docker aplication with external database based on RDS Postgres and Elasticash for Redis as well. 
Test, Build & Push phases were removed from pipeline. You can find them in other versions of the pipelines below.

Source code of this application was taken [here](https://github.com/StephenGrider/multi-docker). [Preview](#preview) and [Diagram](#diagram) of application.

Pipeline for deployment of this application based on Elastic Beanstalk Multi-Docker environment you can find [here](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs).

Pipeline for deployment of this application with Travis-CI to Elastic Beanstalk Multi-Docker environment you can find [here](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-elasticbeanstalk-aws-with-travis).

[Here](https://bitbucket.org/ConstConst/react-app-for-multi-docker-ci-cd-deployment-to/src/master/) you can find the pipeline of this application on Bitbacket git platform.

**Services** required for applying of this version of pipeline:
- User whith programmatic access to AWS,
- Cluster in ECS with adjusted service for deploy task definition,
- Task-definition,
- Dockerhub account for images,
- RDS Postgres DB 
- ElastiCash for Redis.

__*Please note that you could be charged for use of those services.__

Also, following **secrets** should be added to repositories settings required for workfow:
- for AWS configure:
  - aws-access-key-id
  - aws-secret-access-key
  - aws-region
- for Dockerhub login:
  - DOCKER_ID
  - DOCKER_PASSWORD
  
  ## preview
![Preview](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs/blob/master/images/Fibonacci_calculator.png)

## diagram
![Diagram](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs/blob/master/images/AWS%20Multi-container%20Docker%20Application.png)

