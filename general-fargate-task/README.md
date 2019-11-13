# general fargate task

# Useful Commands

```bash
# create Cluster, Task Definition
STACK_NAME=my-fargate-task-v1
aws cloudformation deploy --template-file ./template.yaml --capabilities CAPABILITY_NAMED_IAM --stack-name $STACK_NAME

# setup parameters
CLUSTER_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::ECS::Cluster") | .PhysicalResourceId')
TASK_DEFINITION=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::ECS::TaskDefinition") | .PhysicalResourceId')
SUBNET=subnet-xxx
SECURITY_GROUP=sg-xxx

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
aws ecs run-task --cli-input-json file://./$JSON_FILENAME

# remove parameter.json
rm $JSON_FILENAME

# clean up resources
aws cloudformation delete-stack --stack-name $STACK_NAME
```