# create-random-secure-string

# Useful Commands

```bash
# deploy cloudformation stack
STACK_NAME=my-random-secure-string
TEMPLATE_NAME=template.yaml
aws cloudformation deploy --template-file ./$TEMPLATE_NAME --stack-name $STACK_NAME

# set Secret ARN
SECRET_ID=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME --query 'StackResources[0].PhysicalResourceId' --output text)

# get secret 
# ex:
# {
#     "ARN": "arn:aws:secretsmanager:ap-northeast-1:000000000000:secret:MySecret-3cZXRzsFW9EQ-S5ywYG",
#     "Name": "MySecret-3cZXRzsFW9EQ",
#     "VersionId": "b8aaabd8-0c54-421f-b5e5-ccb29f376163",
#     "SecretString": "{\"password\":\",Dw6\\\"~3C~9FN*%rU(\\\"V2Awo*in9E(^de\",\"username\":\"test-user\"}",
#     "VersionStages": [
#         "AWSCURRENT"
#     ],
#     "CreatedDate": 1573272203.865
# }
aws secretsmanager get-secret-value --secret-id $SECRET_ID

# get SecretString json
# ex:
# {
#   "password": ",Dw6\"~3C~9FN*%rU(\"V2Awo*in9E(^de",
#   "username": "test-user"
# }
aws secretsmanager get-secret-value --secret-id $SECRET_ID --query SecretString | jq fromjson

# get raw SecretString
# ex:
# ,Dw6"~3C~9FN*%rU("V2Awo*in9E(^de
aws secretsmanager get-secret-value --secret-id $SECRET_ID --query SecretString | jq fromjson | jq -r '.password'

# cleanup resources
aws cloudformation delete-stack --stack-name $STACK_NAME
```