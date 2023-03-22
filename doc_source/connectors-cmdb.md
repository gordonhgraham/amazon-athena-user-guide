# Amazon Athena AWS CMDB connector<a name="connectors-cmdb"></a>

The Amazon Athena AWS CMDB connector enables Athena to communicate with various AWS services so that you can query them with SQL\.

## Prerequisites<a name="connectors-cmdb-prerequisites"></a>
+ Deploy the connector to your AWS account using the Athena console or the AWS Serverless Application Repository\. For more information, see [Deploying a connector and connecting to a data source](connect-to-a-data-source-lambda.md) or [Using the AWS Serverless Application Repository to deploy a data source connector](connect-data-source-serverless-app-repo.md)\.

## Parameters<a name="connectors-cmdb-parameters"></a>

Use the Lambda environment variables in this section to configure the AWS CMDB connector\.
+ **spill\_bucket** – Specifies the Amazon S3 bucket for data that exceeds Lambda function limits\.
+ **spill\_prefix** – \(Optional\) Defaults to a subfolder in the specified `spill_bucket` called `athena-federation-spill`\. We recommend that you configure an Amazon S3 [storage lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) on this location to delete spills older than a predetermined number of days or hours\.
+ **spill\_put\_request\_headers** – \(Optional\) A JSON encoded map of request headers and values for the Amazon S3 `putObject` request that is used for spilling \(for example, `{"x-amz-server-side-encryption" : "AES256"}`\)\. For other possible headers, see [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) in the *Amazon Simple Storage Service API Reference*\.
+ **kms\_key\_id** – \(Optional\) By default, any data that is spilled to Amazon S3 is encrypted using the AES\-GCM authenticated encryption mode and a randomly generated key\. To have your Lambda function use stronger encryption keys generated by KMS like `a7e63k4b-8loc-40db-a2a1-4d0en2cd8331`, you can specify a KMS key ID\.
+ **disable\_spill\_encryption** – \(Optional\) When set to `True`, disables spill encryption\. Defaults to `False` so that data that is spilled to S3 is encrypted using AES\-GCM – either using a randomly generated key or KMS to generate keys\. Disabling spill encryption can improve performance, especially if your spill location uses [server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html)\.
+ **default\_ec2\_image\_owner** – \(Optional\) When set, controls the default Amazon EC2 image owner that filters [Amazon Machine Images \(AMI\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)\. If you do not set this value and your query against the EC2 images table does not include a filter for owner, your results will include all public images\.

## Databases and tables<a name="connectors-cmdb-databases-and-tables"></a>

The Athena AWS CMDB connector makes the following databases and tables available for querying your AWS resource inventory\. For more information on the columns available in each table, run a `DESCRIBE database.table` statement using the Athena console or API\.
+ **ec2** – This database contains Amazon EC2 related resources, including the following\.
+ **ebs\_volumes** – Contains details of your Amazon EBS volumes\.
+ **ec2\_instances** – Contains details of your EC2 Instances\.
+ **ec2\_images** – Contains details of your EC2 Instance images\.
+ **routing\_tables** – Contains details of your VPC Routing Tables\.
+ **security\_groups** – Contains details of your security groups\.
+ **subnets** – Contains details of your VPC Subnets\.
+ **vpcs** – Contains details of your VPCs\.
+ **emr** – This database contains Amazon EMR related resources, including the following\.
+ **emr\_clusters** – Contains details of your EMR Clusters\.
+ **rds** – This database contains Amazon RDS related resources, including the following\.
+ **rds\_instances** – Contains details of your RDS Instances\.
+ **s3** – This database contains RDS related resources, including the following\.
+ **buckets** – Contains details of your Amazon S3 buckets\.
+ **objects** – Contains details of your Amazon S3 objects, excluding their contents\.

## Required Permissions<a name="connectors-cmdb-required-permissions"></a>

For full details on the IAM policies that this connector requires, review the `Policies` section of the [athena\-aws\-cmdb\.yaml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-aws-cmdb/athena-aws-cmdb.yaml) file\. The following list summarizes the required permissions\.
+ **Amazon S3 write access** – The connector requires write access to a location in Amazon S3 in order to spill results from large queries\.
+ **Athena GetQueryExecution** – The connector uses this permission to fast\-fail when the upstream Athena query has terminated\.
+ **S3 List** – The connector uses this permission to list your Amazon S3 buckets and objects\.
+ **EC2 Describe** – The connector uses this permission to describe resources such as your Amazon EC2 instances, security groups, VPCs, and Amazon EBS volumes\.
+ **EMR Describe / List** – The connector uses this permission to describe your EMR clusters\.
+ **RDS Describe** – The connector uses this permission to describe your RDS Instances\.

## Performance<a name="connectors-cmdb-performance"></a>

Currently, the Athena AWS CMDB connector does not support parallel scans\. Predicate pushdown is performed within the Lambda function\. Where possible, partial predicates are pushed to the services being queried\. For example, a query for the details of a specific Amazon EC2 instance calls the EC2 API with the specific instance ID to run a targeted describe operation\.

## License information<a name="connectors-cmdb-license-information"></a>

The Amazon Athena AWS CMDB connector project is licensed under the [Apache\-2\.0 License](https://www.apache.org/licenses/LICENSE-2.0.html)\.

## See also<a name="connectors-cmdb-see-also"></a>

For additional information about this connector, visit [the corresponding site](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-aws-cmdb) on GitHub\.com\.