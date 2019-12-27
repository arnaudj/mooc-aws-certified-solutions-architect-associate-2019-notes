# CHAPTER 8 | Application Services

## [SQS](https://aws.amazon.com/sqs/)

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications.

* Message queue service
* Retention 1min to 14 days (default: 4 days) 
* SQS is pull based, not push based. Supports short and long polling
* Messages size is up to 256KB
* Visibility timeout (max 12 hours): delay after which a message read from SQS but 'not yet confirmed as fully processed by this consumer' will become visible again by another consumer.
* Type of queues:
  * Default: best effort ordering, highly parallel, at least once but not exactly once
  * FIFO: (first in first out): ordering guaranteed, exactly once delivery
* You can have duplicates if you don't manage very well your visibility timeouts.

_Read the [SQS faqs](https://aws.amazon.com/sqs/faqs/) before taking the exam._

## [SWF](https://aws.amazon.com/swf/)

Amazon SWF (Simple Workflow Service) is an Amazon Web Services tool that helps developers coordinate, track and audit multi-step, multi-machine application jobs.

* To develop workflows (ex: delivering a book from AMZ warehouse)
* SWF vs SQS:
  * Retention: 1 year for workflows (SWF) vs 14 days for messages (SQS)
  * Task oriented API vs message oriented API
  * Only once assignment and no duplication
  * SWF keeps central track of all tasks/events vs SQS with multiple queues require app level tracking logic

SWF actors:
* SWF workflow starters: initiate a workflow (ex: new e-commerce order)
* SWF Decider: control the flow of tasks in a workflow execution
* SWF Workers: carry out the tasks

## [SNS](https://aws.amazon.com/sns/)

Amazon SNS enables message filtering and fan out to a large number of subscribers, including serverless functions, queues, and distributed systems. Additionally, Amazon SNS fans out notifications to end users via mobile push messages, SMS, and email.

* In the exams it will be around Auto Scaling questions.
* SNS stores copies of the messages on multiple AZ.
* Push-based
* It can notify through:
  * AWS Lambda functions
  * HTTP/S webhooks
  * mobile push
  * SMS
  * email

## [Elastic Transcoder](https://aws.amazon.com/elastictranscoder/)

Amazon Elastic Transcoder a is media transcoding service.

## [API Gateway](https://aws.amazon.com/api-gateway/)

Amazon API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor, and secure APIs at any scale.

* You can enable cache
* Scaled automatically
* You can throttle requests
* Connect to CloudWatch to log all requests.

## [Kinesis](https://aws.amazon.com/kinesis/)

Amazon Kinesis makes it easy to collect, process, and analyze real-time, streaming data so you can get timely insights and react quickly to new information.

* Kinesis streams: It consists of shards, where each stream is saved and stored for 24h, up to 7 days. You can have multiple shards on each stream. A consumer then read from the shards and process the data.
* Kinesis Firehose: It doesn't have shards and their built-in persistence. So data traverses the Firehose. Data has to be processed immediately with lambda or stored in S3 for example.
  * It can stream data to Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk.
* Kinesis Analytics: It allows you to analyze the data that exists in Kinesis Firehose of streams.

## [Cognito](https://aws.amazon.com/cognito/)
Provides identity federation: allows users to authenticate via a Web Identity Provider (Google, Facebook, Amazon)


Exam tips:
* First, the user identifies with the WebId provider via the **user pool**
* Then the AWS credentials (IAM role) are retrieved via the **identity pool**

User pool is used based, handles: registration, authentication, account recovery.

Identity pool authorizes access to the AWS resources.