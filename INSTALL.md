The infrastructure needed for this project is AWS ECS, using AWS Fargate. 

## Configure your environment
In order to build this project, you'll need to run on Linux, OSX or an AWS Cloud9 instance
```
pip3 install awscli --upgrade --user 
sudo apt install jq gettext
curl -so ~/bin/ecs-cli https://s3.amazonaws.com/amazon-ecs-cli/ecs-cli-linux-amd64-latest
chmod +x ~/bin/ecs-cli
aws configure
# Make sure to properly configure your aws cli tool
```

## Build VPC, ECS Cluster, and ALB for both production and acceptance environments
```
mu env up -A
```

## Build API and Database tier in AWS for our acceptance environment
```
cd api
# Create Database tier
mu db up <environment>
# create ecr image and api service with task
mu service push <environment>
mu service deploy <environment> 
```
This step creates a database for for the API service, then
builds the api docker image and pushes it to a newly created ECR repository.
A ECS service gets created and named mu-service-api-acceptance-EcsService, with an
attached service task definition named mu-servce-api-acceptance which references the
container image that was pushed to ECR. 

### Create CI/CD pipeline for API tier 
```
# from inside the api/ directory
# initalize the pipeline, will take about 10min
mu pipeline up
# See the pipeline that mu created
mu svc show 
Pipeline URL:   https://console.aws.amazon.com/codesuite/codepipeline/pipelines/mu-web/view?region=us-west-2
+------------+----------+------------------------------------------+-------------+---------------------+
|   STAGE    |  ACTION  |                 REVISION                 |   STATUS    |     LAST UPDATE     |
+------------+----------+------------------------------------------+-------------+---------------------+
| Source     | Source   | 8383df64bbbae99e318d9a9d5ec4f83ed1c91e3f | Succeeded   | 2019-07-18 19:58:42 |
| Build      | Artifact |                                        - | Succeeded   | 2019-07-18 19:59:15 |
| Build      | Image    |                                        - | Succeeded   | 2019-07-18 20:02:24 |
| Acceptance | Deploy   |                                        - | Succeeded   | 2019-07-18 20:08:08 |
| Acceptance | Test     |                                        - | Succeeded   | 2019-07-18 20:08:42 |
| Production | Approve  |                                        - | InProgress  | 0001-01-01 01:16:20 |
| Production | Deploy   |                                        - | -           |                   - |
| Production | Test     |                                        - | -           |                   - |
+------------+----------+------------------------------------------+-------------+---------------------+
cd ../
```
In this step we build a pipeline for the API service. Our github repo is polled for changes
by AWS CodeBuild and is triggered on each commit. Once the change is detected AWS CodeBuild 
builds an artifact from the repo, then builds the api container image and pushes to the ECR
repository. Provied all tests have passed the pipeline deploys the latest api build to the 
acceptance environment's ECS cluster. The pipeline then waits for promotion from the 
acceptance environment to the production environment. The approval process can be hanled in AWS
CodeBuild dashboard. 


## Create the Web tier
Follow the same steps for the web service
```
cd web
mu service push acceptance
mu service deploy acceptance
mu pipeline up
cd ../
```
This creates the web container, web task and web service as well as the pipeline for building the web container in the AWS acceptance environment. It's necessary to create the web tier after the api and database tier because the health of th web container relys on recieving a response from the api service. 

## Create Cloudfront distribution to act as our CDN for both environments
```
# in the root of the project directory
ACCEPTANCE_DNS=$(mu env show acceptance | grep 'Base URL'| cut -f3 -d'/')
aws cloudfront create-distribution --origin-domain-name $ACCEPTANCE_DNS | jq '.Distribution["DomainName"]'
PROD_DNS=$(mu env show acceptance | grep 'Base URL' | cut -f3 -d'/')
aws cloudfront create-distribution --origin-domain-name $ACCEPTANCE_DNS | jq '.Distribution["DomainName"]'
```
This step grabs the application load balancer url for each environment (acceptance/production) and then creates a CloudFront distribution that spans across all regions.



