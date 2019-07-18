The infrastructure needed for this project is AWS ECS, using AWS Fargate. This directory contains the needed scripts build and deploy the infrastructue to your AWS account. 

## Configure your console environment
```
pip3 install awscli --upgrade --user 
sudo apt install jq gettext
curl -so ~/bin/ecs-cli https://s3.amazonaws.com/amazon-ecs-cli/ecs-cli-linux-amd64-latest
chmod +x ~/bin/ecs-cli
aws configure
# Make sure to properly configure your aws cli tool
```

## Build VPC, ECS Cluster, and ALB
```
aws cloudformation create-stack --stack-name test-stack --template-body file://$PWD/cluster-node-3tier-app-vpc.yml --region us-west-2 --capabilities CAPABILITY_IAM

aws cloudformation create-stack --stack-name node-3tier-app-alb --template-body file://$PWD/alb-config.yml --region us-west-2

cd ../
mu env up -A
```

## Create API servie and deploy the api application
