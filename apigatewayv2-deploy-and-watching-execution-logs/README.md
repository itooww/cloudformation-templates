# API Gateway V2 deploy and watching execution logs

# Useful Commands

```bash
# deploy
STACK_NAME=mystack
aws cloudformation deploy --template-file ./template.yaml --stack-name $STACK_NAME

# get connection parameter
REGION=ap-northeast-1
API_ID=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::ApiGatewayV2::Api") | .PhysicalResourceId')
STAGE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType == "AWS::ApiGatewayV2::Stage") | .PhysicalResourceId')

# web socket connection
wscat -c wss://$API_ID.execute-api.$REGION.amazonaws.com/$STAGE_NAME

# clean up
aws cloudformation delete-stack --stack-name $STACK_NAME
```

# Links

Amazon API Gateway V2 リソースタイプのリファレンス - AWS CloudFormation  
https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/AWS_ApiGatewayV2.html

apigatewayv2 — AWS CLI 1.16.290 Command Reference  
https://docs.aws.amazon.com/cli/latest/reference/apigatewayv2/index.html

Amazon API Gateway Version 2 API Reference - Amazon API Gateway  
https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/api-reference.html

Amazon API Gateway での WebSocket API の作成、デプロイ、および呼び出し - Amazon API Gateway  
https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/apigateway-websocket-api.html