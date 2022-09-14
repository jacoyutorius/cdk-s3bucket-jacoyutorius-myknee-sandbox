# `cdk import` を試したリポジトリ

---


# cdk importを試す

## 参考

[https://zenn.dev/xeres/articles/2022-04-12-cdk-import-preview#再度-cdk-bootstrap-をする-(既存プロジェクトのみ)](https://zenn.dev/xeres/articles/2022-04-12-cdk-import-preview#%E5%86%8D%E5%BA%A6-cdk-bootstrap-%E3%82%92%E3%81%99%E3%82%8B-(%E6%97%A2%E5%AD%98%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%AE%E3%81%BF))

---

## ログ


```bash
$ mkdir cdk-s3bucket-jacoyutorius-myknee-sandbox

$ cd cdk-s3bucket-jacoyutorius-myknee-sandbox

$ cdk init  --language=typescript
Applying project template app for typescript
# Welcome to your CDK TypeScript project

This is a blank project for CDK development with TypeScript.

The `cdk.json` file tells the CDK Toolkit how to execute your app.

## Useful commands

* `npm run build`   compile typescript to js
* `npm run watch`   watch for changes and compile
* `npm run test`    perform the jest unit tests
* `cdk deploy`      deploy this stack to your default AWS account/region
* `cdk diff`        compare deployed stack with current state
* `cdk synth`       emits the synthesized CloudFormation template

Initializing a new git repository...
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint:
hint: 	git config --global init.defaultBranch <name>
hint:
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint:
hint: 	git branch -m <name>
Executing npm install...
✅ All done!
```

一旦、中身がからの状態で deployする。

importするための空のスタックを作る感じ。

```tsx
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';

export class CdkS3BucketJacoyutoriusSandboxStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);
  }
}
```

```bash

$ cdk deploy

✨  Synthesis time: 10.05s

CdkS3BucketJacoyutoriusSandboxStack: building assets...

[0%] start: Building 25fb36e740821e076ec4f96788844a26610b03a98be16616c727f8184d5ce200:current_account-current_region
[100%] success: Built 25fb36e740821e076ec4f96788844a26610b03a98be16616c727f8184d5ce200:current_account-current_region

CdkS3BucketJacoyutoriusSandboxStack: assets built

CdkS3BucketJacoyutoriusSandboxStack: deploying...
[0%] start: Publishing 25fb36e740821e076ec4f96788844a26610b03a98be16616c727f8184d5ce200:current_account-current_region
[100%] success: Published 25fb36e740821e076ec4f96788844a26610b03a98be16616c727f8184d5ce200:current_account-current_region
CdkS3BucketJacoyutoriusSandboxStack: creating CloudFormation changeset...

 ✅  CdkS3BucketJacoyutoriusSandboxStack

✨  Deployment time: 18.93s

Stack ARN:
arn:aws:cloudformation:ap-northeast-1:************:stack/CdkS3BucketJacoyutoriusSandboxStack/f856cba0-3471-11ed-85c5-0a55129c2e89

✨  Total time: 28.98s
```

S3バケットを作成するコードを追記。

```tsx
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as s3 from 'aws-cdk-lib/aws-s3'

export class CdkS3BucketJacoyutoriusSandboxStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    const bucket = new s3.Bucket(this, 'jacoyutorius-sandbox')
  }
}
```

`cdk import` を実施。

```bash
$ cdk import
The 'cdk import' feature is currently in preview.
CdkS3BucketJacoyutoriusSandboxStack

User: arn:aws:sts::************:assumed-role/cdk-hnb659fds-deploy-role-************-ap-northeast-1/aws-cdk-user is not authorized to perform: cloudformation:GetTemplateSummary because no identity-based policy allows the cloudformation:GetTemplateSummary action
```

権限エラーになったのでbootstrapし直す。

```bash
$ npx cdk bootstrap
 ⏳  Bootstrapping environment aws://************/ap-northeast-1...
Trusted accounts for deployment: (none)
Trusted accounts for lookup: (none)
Using default execution policy of 'arn:aws:iam::aws:policy/AdministratorAccess'. Pass '--cloudformation-execution-policies' to customize.
CDKToolkit: creating CloudFormation changeset...
 ✅  Environment aws://************/ap-northeast-1 bootstrapped.

```

再度import。

```bash
$ cdk import
The 'cdk import' feature is currently in preview.
CdkS3BucketJacoyutoriusSandboxStack
CdkS3BucketJacoyutoriusSandboxStack/jacoyutorius-sandbox/Resource (AWS::S3::Bucket): enter BucketName to import (empty to skip): jacoyutorius-sandbox
CdkS3BucketJacoyutoriusSandboxStack: importing resources into stack...
CdkS3BucketJacoyutoriusSandboxStack: creating CloudFormation changeset...

 ✅  CdkS3BucketJacoyutoriusSandboxStack
Import operation complete. We recommend you run a drift detection operation to confirm your CDK app resource definitions are up-to-date. Read more here: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/detect-drift-stack.html
```
