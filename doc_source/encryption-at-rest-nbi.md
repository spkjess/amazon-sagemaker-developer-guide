# Notebook instances and SageMaker jobs<a name="encryption-at-rest-nbi"></a>

To encrypt the machine learning \(ML\) storage volume that is attached to notebooks, processing jobs, training jobs, hyperparameter tuning jobs, batch transform jobs, and endpoints, you can pass a AWS KMS key to SageMaker\. If you don't specify a KMS key, SageMaker encrypts storage volumes with a transient key and discards it immediately after encrypting the storage volume\. For notebook instances, if you don't specify a KMS key, SageMaker encrypts both OS volumes and ML data volumes with a system\-managed KMS key\.

You can use an AWS managed AWS KMS key to encrypt all instance OS volumes\. You can encrypt all ML data volumes for all SageMaker instances with a AWS KMS key that you specify\. ML storage volumes are mounted as follows:
+ Notebooks \- `/home/ec2-user/SageMaker`
+ Processing \- `/opt/ml/processing` and `/tmp/` 
+ Training \- `/opt/ml/` and `/tmp/`
+  Batch \- `/opt/ml/` and `/tmp/`
+ Endpoints \- `/opt/ml/` and `/tmp/` 

Processing, batch transform, and training job containers and their storage are ephemeral in nature\. When the job completes, output is uploaded to Amazon S3 using AWS KMS encryption with an optional AWS KMS key that you specify and the instance is torn down\. If a AWS KMS Key is not provided in the job request, the output is encrypted by default using your AWS Managed Key for Amazon S3\.

**Note**  
The key policy for an AWS Managed Key for Amazon S3 cannot be edited, so cross\-account permissions cannot be granted for these key policies\. If the output Amazon S3 bucket for the request is from another account, specify your own AWS KMS Customer Key in the job request and ensure that the job's execution role has permissions to encrypt data with it\.

**Important**  
Sensitive data that needs to be encrypted with a KMS key for compliance reasons should be stored in the ML storage volume or in Amazon S3, both of which can be encrypted using a KMS key you specify\. 

When you open a notebook instance, SageMaker saves it and any files associated with it in the SageMaker folder in the ML storage volume by default\. When you stop a notebook instance, SageMaker creates a snapshot of the ML storage volume\. Any customizations to the operating system of the stopped instance, such as installed custom libraries or operating system level settings, are lost\. Consider using a lifecycle configuration to automate customizations of the default notebook instance\. When you terminate an instance, the snapshot and the ML storage volume are deleted\. Any data that you need to persist beyond the lifespan of the notebook instance should be transferred to an Amazon S3 bucket\.

**Note**  
Certain Nitro\-based SageMaker instances include local storage, depending on the instance type\. Local storage volumes are encrypted using a hardware module on the instance\. You can't use a KMS key on an instance type with local storage\. For a list of instance types that support local instance storage, see [Instance Store Volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes)\. For more information about storage volumes on Nitro\-based instances, see [Amazon EBS and NVMe on Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/nvme-ebs-volumes.html)\.  
For more information about local instance storage encryption, see [SSD Instance Store Volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ssd-instance-store.html)\.