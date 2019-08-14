# cloudformation-templates

- my cloudformation snippets

## How to Deploy CloudFormation Templates

```bash
$ aws cloudformation validate-template --template-body file://./xxxx.yaml
$ aws cloudformation deploy --template-file ./xxxx.yaml --stack-name mystack
$ aws cloudformation describe-stack-events --stack-name mystack
$ aws cloudformation describe-stack-resources --stack-name mystack
$ aws cloudformation delete-stack --stack-name mystack
```
