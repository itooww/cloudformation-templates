# ecr push docker

# Useful Commands

```bash
# deploy ecr repository
STACK_NAME=my-ecr
aws cloudformation deploy --template-file ./template.yaml --capabilities CAPABILITY_IAM --stack-name $STACK_NAME

# ecr login
REGION=$(aws configure get region) # get aws cli config region
$(aws ecr get-login --no-include-email --region $REGION)

# get ecr repository name
ECR_REPOSITORY_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::ECR::Repository") | .PhysicalResourceId')

# get AWS Account ID
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

# make ECR Repository URL
ECR_REPOSITORY_URL=$AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com

# docker image build, push
IMAGE_TAG=latest
docker build -t $ECR_REPOSITORY_NAME .
docker tag $ECR_REPOSITORY_NAME:$IMAGE_TAG $ECR_REPOSITORY_URL/$ECR_REPOSITORY_NAME:$IMAGE_TAG
docker push $ECR_REPOSITORY_URL/$ECR_REPOSITORY_NAME:$IMAGE_TAG

# get image detail
aws ecr describe-images --repository-name $ECR_REPOSITORY_NAME
aws ecr batch-get-image --repository-name $ECR_REPOSITORY_NAME --image-ids imageTag=$IMAGE_TAG

# prerry print imageManifest
aws ecr batch-get-image --repository-name $ECR_REPOSITORY_NAME --image-ids imageTag=$IMAGE_TAG | jq '.images[0].imageManifest | fromjson'

# delete images
aws ecr batch-delete-image --repository-name $ECR_REPOSITORY_NAME --image-ids imageTag=$IMAGE_TAG

# cleanup resources
aws cloudformation delete-stack --stack-name $STACK_NAME
```