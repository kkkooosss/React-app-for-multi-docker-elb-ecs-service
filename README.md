![Deploy to Amazon ECS with ecs-cli & Elastic Beanstalk](https://github.com/kkkooosss/React-app-for-multi-docker-CI-CD-deployment-to-ecs/workflows/Deploy%20to%20Amazon%20ECS%20with%20ecs-cli%20&%20Elastic%20Beanstalk/badge.svg)

# **React-app-for-multi-docker-CI-CD-deployment-ECS-with-github-actions**

This CI/CD pipeline uses GitHub Actions which makes a deploymeny of new revision of sample multi container Docker aplication with external database based on RDS Postgres and Elasticash for Redis as well. 

Source code of this application was taken [here](https://github.com/StephenGrider/multi-docker). [Preview](#preview) and [Diagram](#diagram) of application.

Deployment based on Elastic Beanstalk update environment after update of all Docker images on Dockerhub.

**Services** required for applying of this pipeline:
- User whith programmatic access to AWS,
- Cluster in ECS,
- Elastic Beanstalk with created Application and Environment for Multi container Docker App,
- S3 for keeping zip files for Elastic Beanstalk,   
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
- Postgres DB credentials:
  - POSTGRES_USER
  - POSTGRES_PASSWORD  



