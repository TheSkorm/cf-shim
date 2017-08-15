CloudFormation Boto3 Shim Thing
==
A CloudFormation template that allows you to use the latest basic boto3 methods to create AWS resources.

Use cases
--
Sometimes when new resources are added to Amazon they don't ship with CloudFormation support or sometimes you need to create resources in another region (such as ACM certs in us-east-1).

CustomResource Example
--

```yaml
Resources:
    ResourceName: # in this example we will create an SNS topic without using CloudFormation prematives
        Type: Custom::CFShim # Required by AWS
        DependsOn: [ShimLambda] # Ensure that the Shim is loaded first
        Properties:
            ServiceToken: !GetAtt ShimLambda.Arn # Pointer to the shim
            Service: "sns" # The name of the boto3 client class http://boto3.readthedocs.io/en/latest/reference/services/index.html
            Create: "create_topic" # the method name that you want to call on create eg http://boto3.readthedocs.io/en/latest/reference/services/sns.html#SNS.Client.create_topic
            PhysicalResourceId: "TopicArn" #this is the variable from the boto3 method response that sets the physicalresourceid - you can use this as parameters in update and delete stack by using "$PhysicalResourceId$"
            Delete: "delete_topic" # the method name that you want to call on delete
            Update: "set_topic_attributes" # the method name that you want to call on update
            CreateData:  #the data that will be fed to the boto3 method
                Name: "TestTopicNameTest" 
            DeleteData: #the data that will be fed to the boto3 method
                TopicArn: "$PhysicalResourceId$"
            UpdateData: #the data that will be fed to the boto3 method
                TopicArn: "$PhysicalResourceId$"
                AttributeName: "DisplayName"
                AttributeValue: "TestUpdate4"
```

Usage
--

 1. Take from example.yml the ShimLambda resource. Also create a role for that lambda to use
 2. Define your resources using the examples from the bottom (or above) of example.yml config
