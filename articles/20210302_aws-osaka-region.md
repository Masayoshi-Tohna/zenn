---
title: "AWS東京リージョンと大阪リージョンの比較"
emoji: "🎉"
type: "tech"
topics: ["AWS"]
published: true
---

## AWS大阪リージョンが正式オープン

2021/03/02、AWS大阪ローカルリージョンが拡張される形で、国内2拠点目となるAWSリージョン「大阪リージョン（ap-northeast-3）」がオープンしました。

参考：[AWS、国内 2 拠点目となるリージョンを開設 \| AWS](https://aws.amazon.com/jp/about-aws/whats-new/2021/03/osaka-becomes-second-standard-region-in-japan/)

## 機能差異サマリ

日々アップデートされているとされる[AWS Regional Services](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/?nc1=h_ls)を参考に、提供される機能の比較をしたところ、大阪リージョンはまだまだ機能が少ないようです。

Region | Num
-- | --
Tokyo | 168
Osaka | 54

## 機能差異一覧

有名どころだと、AWS Auto ScalingやAWS Security Hub、AWS Transit Gatewayなどが大阪リージョンにはないようです。

service | tokyo | osaka
-- | :--: | :--:
AWS Amplify | ○ | －
AWS App Mesh | ○ | －
AWS AppSync | ○ | －
AWS Application Discovery Service | ○ | －
AWS Artifact | ○ | ○
AWS Audit Manager | ○ | －
AWS Auto Scaling | ○ | －
AWS Backup | ○ | －
AWS Batch | ○ | －
AWS Budgets | ○ | －
AWS Certificate Manager | ○ | ○
AWS Chatbot | ○ | －
AWS Cloud Map | ○ | －
AWS Cloud9 | ○ | －
AWS CloudFormation | ○ | ○
AWS CloudHSM | ○ | －
AWS CloudTrail | ○ | ○
AWS CodeArtifact | ○ | －
AWS CodeBuild | ○ | －
AWS CodeCommit | ○ | －
AWS CodeDeploy | ○ | ○
AWS CodePipeline | ○ | －
AWS CodeStar | ○ | －
AWS Compute Optimizer | ○ | －
AWS Config | ○ | ○
AWS Data Exchange | ○ | －
AWS Data Pipeline | ○ | －
AWS DataSync | ○ | －
AWS Database Migration Service | ○ | ○
AWS DeepLens | ○ | －
AWS Direct Connect | ○ | ○
AWS Directory Service | ○ | －
AWS Elastic Beanstalk | ○ | ○
AWS Elemental MediaConnect | ○ | －
AWS Elemental MediaConvert | ○ | －
AWS Elemental MediaLive | ○ | －
AWS Elemental MediaPackage | ○ | －
AWS Elemental MediaStore | ○ | －
AWS Elemental MediaTailor | ○ | －
AWS Fargate | ○ | ○
AWS Firewall Manager | ○ | －
AWS Global Accelerator | ○ | －
AWS Glue | ○ | ○
AWS IQ | ○ | ○
AWS Identity and Access Management (IAM) | ○ | ○
AWS IoT 1-Click | ○ | －
AWS IoT Analytics | ○ | －
AWS IoT Core | ○ | －
AWS IoT Device Defender | ○ | －
AWS IoT Device Management | ○ | －
AWS IoT Events | ○ | －
AWS IoT Greengrass | ○ | －
AWS IoT Things Graph | ○ | －
AWS Key Management Service | ○ | ○
AWS Lake Formation | ○ | －
AWS Lambda | ○ | ○
AWS License Manager | ○ | －
AWS Managed Services | ○ | －
AWS Marketplace | ○ | ○
AWS Migration Hub | ○ | －
AWS OpsWorks Stacks | ○ | －
AWS OpsWorks for Chef Automate | ○ | －
AWS OpsWorks for Puppet Enterprise | ○ | －
AWS Organizations | ○ | ○
AWS Outposts | ○ | －
AWS Personal Health Dashboard | ○ | ○
AWS PrivateLink | ○ | ○
AWS Proton | ○ | －
AWS Resource Access Manager (RAM) | ○ | －
AWS RoboMaker | ○ | －
AWS Secrets Manager | ○ | ○
AWS Security Hub | ○ | －
AWS Server Migration Service (SMS) | ○ | －
AWS Serverless Application Repository | ○ | －
AWS Service Catalog | ○ | －
AWS Shield | ○ | －
AWS Single Sign-On | ○ | －
AWS Snowball | ○ | ○
AWS Step Functions | ○ | ○
AWS Storage Gateway | ○ | －
AWS Support | ○ | ○
AWS Systems Manager | ○ | ○
AWS Transfer Family | ○ | －
AWS Transit Gateway | ○ | －
AWS Trusted Advisor | ○ | －
AWS VPN | ○ | ○
AWS WAF | ○ | －
AWS Well-Architected Tool | ○ | －
AWS X-Ray | ○ | ○
Amazon API Gateway | ○ | ○
Amazon AppFlow | ○ | －
Amazon AppStream 2.0 | ○ | －
Amazon Athena | ○ | －
Amazon Augmented AI (A2I) | ○ | －
Amazon Aurora | ○ | ○
Amazon Chime | ○ | －
Amazon CloudFront | ○ | ○
Amazon CloudSearch | ○ | －
Amazon CloudWatch | ○ | ○
Amazon CodeGuru | ○ | －
Amazon Cognito | ○ | －
Amazon Comprehend | ○ | －
Amazon Connect | ○ | －
Amazon Detective | ○ | －
Amazon DevOps Guru | ○ | －
Amazon DocumentDB (with MongoDB   compatibility) | ○ | －
Amazon DynamoDB | ○ | ○
Amazon ElastiCache | ○ | ○
Amazon Elastic Block Store (EBS) | ○ | ○
Amazon Elastic Compute Cloud (EC2) | ○ | ○
Amazon Elastic Container Registry (ECR) | ○ | ○
Amazon Elastic Container Service (ECS) | ○ | ○
Amazon Elastic File System (EFS) | ○ | ○
Amazon Elastic Inference | ○ | －
Amazon Elastic Kubernetes Service (EKS) | ○ | ○
Amazon Elastic MapReduce (EMR) | ○ | ○
Amazon Elastic Transcoder | ○ | －
Amazon Elasticsearch Service | ○ | ○
Amazon EventBridge | ○ | ○
Amazon FSx for Lustre | ○ | －
Amazon FSx for Windows File Server | ○ | －
Amazon Forecast | ○ | －
Amazon GameLift | ○ | －
Amazon GuardDuty | ○ | －
Amazon Inspector | ○ | －
Amazon Keyspaces (for Apache Cassandra) | ○ | －
Amazon Kinesis Data Analytics | ○ | －
Amazon Kinesis Data Firehose | ○ | ○
Amazon Kinesis Data Streams | ○ | ○
Amazon Kinesis Video Streams | ○ | －
Amazon Lex | ○ | －
Amazon Lightsail | ○ | －
Amazon Location Service | ○ | －
Amazon Lookout for Vision | ○ | －
Amazon Lumberyard | ○ | －
Amazon MQ | ○ | －
Amazon Macie | ○ | －
Amazon Managed Blockchain | ○ | －
Amazon Managed Streaming for Apache   Kafka | ○ | －
Amazon Managed Workflows for Apache   Airflow | ○ | －
Amazon Neptune | ○ | －
Amazon Personalize | ○ | －
Amazon Pinpoint | ○ | －
Amazon Polly | ○ | －
Amazon Quantum Ledger Database (QLDB) | ○ | －
Amazon QuickSight | ○ | －
Amazon Redshift | ○ | ○
Amazon Rekognition | ○ | －
Amazon Relational Database Service (RDS) | ○ | ○
Amazon Route 53 | ○ | ○
Amazon SageMaker | ○ | －
Amazon Simple Email Service (SES) | ○ | －
Amazon Simple Notification Service (SNS) | ○ | ○
Amazon Simple Queue Service (SQS) | ○ | ○
Amazon Simple Storage Service (S3) | ○ | ○
Amazon Simple Workflow Service (SWF) | ○ | ○
Amazon Sumerian | ○ | －
Amazon Transcribe | ○ | －
Amazon Transcribe Medical | ○ | －
Amazon Translate | ○ | －
Amazon Virtual Private Cloud (VPC) | ○ | ○
Amazon WorkDocs | ○ | －
Amazon WorkSpaces | ○ | －
CloudEndure Disaster Recovery | ○ | ○
CloudEndure Migration | ○ | ○
Elastic Load Balancing | ○ | ○
FreeRTOS | ○ | －
VMware Cloud on AWS | ○ | －

これからの発展に期待ですね！

## 更新履歴

- 2021/03/05：一覧にはないが、大阪リージョンではCloudShellも使えない模様。
- 2021/03/05：Amazon Elastic File System (EFS)の更新を確認。

以上
