# apigateway deploy with openapi

## これは何

API Gateway で OpenAPI 使ってミニマムなモック API を作成し、 `curl` でリクエストを行って、その結果出力される実行ログを見る。というコマンドとリソースを用意してくれる CloudFormation テンプレート。

前提として、 AWS CLI と awslogs のインストールが必要。あと `pbcopy` コマンド使うので Mac

動かすコマンド

```bash
$ aws cloudformation deploy --template-file ./template.yaml --stack-name mystack
$ aws cloudformation describe-stacks --stack-name apigw-deploy-openapi-6 --query 'Stacks[0].Outputs[0].OutputValue' --output text | pbcopy
# Command + V, Enter
$ curl -v -X GET https://xxxx.execute-api.ap-northeast-1.amazonaws.com/hello/; awslogs get API-Gateway-Execution-Logs_xxxx/hello ALL --watch
```