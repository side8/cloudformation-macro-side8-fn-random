# side8-fn-random CloudFormation macro

## What is it?

A CloudFormation [Transform Macro](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-section-structure.html) that generates random strings or integers.

## Why is it?

Because of a (at time of writing 2 and a half year old) bug in the Serverless API SAM transform `!Ref` or `!GetAtt` changes in the API definition break deployed APIs.

See the following issues about the bug:

* https://github.com/awslabs/serverless-application-model/issues/191
* https://github.com/awslabs/serverless-application-model/issues/660
* https://github.com/awslabs/serverless-application-model/issues/914

## How do I use it?

Once this CloudFormation is deployed to your account, add `Side8-Fn-Random` to your Transform arrays and then use it similar to how you'd use CloudFormation intrinsic functions.

```
"Side8::Fn::Random":
  "Type": "String"|"Integer"
  "Length": integer
```

For example, if you want a 8 character random string in your api's description to force `AWS::Serverless-2016-10-31` to generate a deployment, use it like this:

```
AWSTemplateFormatVersion: 2010-09-09
Transform: ["Side8-Fn-Random", "AWS::Serverless-2016-10-31"]
Resources:
  ServerlessApi:
    Type: AWS::Serverless::Api
    Properties:
      StageName: Prod
      EndpointConfiguration: REGIONAL
      AccessLogSetting: {}
      DefinitionBody:
        swagger: "2.0"
        info:
          version: "2017-04-26T03:23:48Z"
          title: random-api
          description: !Sub
            - random description ${RND}
            - RND:
                Side8::Fn::Random:
                  Type: String
                  Length: 8
...
```
