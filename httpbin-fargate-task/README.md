# httpbin fargate task

# Links

postmanlabs/httpbin: HTTP Request & Response Service, written in Python + Flask.  
https://github.com/postmanlabs/httpbin

kennethreitz/httpbin - Docker Hub  
https://hub.docker.com/r/kennethreitz/httpbin


# Useful Commands

```bash
# create Cluster, Task Definition, Security Group
STACK_NAME=my-httpbin-fargate-task-v2
VPC_ID=vpc-xxx
aws cloudformation deploy --template-file ./template.yaml --capabilities CAPABILITY_NAMED_IAM --stack-name $STACK_NAME --parameter-overrides VPCID=$VPC_ID

# setup parameters
CLUSTER_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::ECS::Cluster") | .PhysicalResourceId')
TASK_DEFINITION=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::ECS::TaskDefinition") | .PhysicalResourceId')
SECURITY_GROUP=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::EC2::SecurityGroup") | .PhysicalResourceId')
SUBNET=subnet-xxx

# generate run-task parameter.json
JSON_FILENAME=parameter.json
cat << EOS > $JSON_FILENAME
{
    "cluster": "${CLUSTER_NAME}",
    "taskDefinition": "${TASK_DEFINITION}",
    "count": 1,
    "launchType": "FARGATE",
    "networkConfiguration": {
        "awsvpcConfiguration": {
            "subnets": [
                "${SUBNET}"
            ],
            "securityGroups": [
                "${SECURITY_GROUP}"
            ],
            "assignPublicIp": "ENABLED"
        }
    }
}
EOS

# ran-task
aws ecs run-task --cli-input-json file://./$JSON_FILENAME.

# get httpbin ip
TASK_ARN=$(aws ecs list-tasks --cluster $CLUSTER_NAME | jq -r '.taskArns[0]')
ENI_ID=$(aws ecs describe-tasks --cluster $CLUSTER_NAME --tasks $TASK_ARN | jq -r '.tasks[0].attachments[0].details[] | select(.name == "networkInterfaceId") | .value')
MY_HTTPBIN_IP=$(aws ec2 describe-network-interfaces --network-interface-ids $ENI_ID | jq -r '.NetworkInterfaces[0].Association.PublicIp')

# curl sample
curl http://$MY_HTTPBIN_IP/get

# stop task
aws ecs stop-task --cluster $CLUSTER_NAME --task $TASK_ARN

# remove parameter.json
rm $JSON_FILENAME

# clean up resources
aws cloudformation delete-stack --stack-name $STACK_NAME
```