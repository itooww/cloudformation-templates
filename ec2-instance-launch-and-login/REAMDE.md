# ec2 instance launch and login

# Useful Commands

```bash
STACK_NAME=mystackv6

# deploy
aws cloudformation deploy --template-file ./template.yaml --stack-name $STACK_NAME --capabilities CAPABILITY_IAM

# deploy with overrides parameter
EC2_IMAGE_ID=ami-xxx
EC2_INSTANCE_TYPE=t2.micro
aws cloudformation deploy --template-file ./template.yaml --stack-name $STACK_NAME --capabilities CAPABILITY_IAM --parameter-overrides Ec2ImageId=$EC2_IMAGE_ID Ec2InstanceType=$EC2_INSTANCE_TYPE

# get instance id
INSTANCE_ID=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::EC2::Instance") | .PhysicalResourceId')

# login to instance
aws ssm start-session --target $INSTANCE_ID

# clean up
aws cloudformation describe-stack-resources --stack-name $STACK_NAME
```