Architecture of Serverless CRUD function is 

![Diagram 1](images/Architecture.png)

An Amazon API Gateway is a collection of resources and methods. In this demo, I created one resource (DynamoDBManager) and define one method (POST) on it. The method is backed by a Lambda function (LambdaFunctionOverHttps). That is, when I call the API through an HTTPS endpoint, Amazon API Gateway invokes the Lambda function.

The POST method on the DynamoDBManager resource supports the following DynamoDB operations:

Create, update, and delete an item.
Read an item.
Scan an item.
Other operations (echo, ping), not related to DynamoDB, that I can use for testing.

The following is a sample request payload for a DynamoDB create item operation:

```json

{
    "operation": "create",
    "tableName": "lambda-apigateway",
    "payload": {
        "Item": {
            "id": "1",
            "name": "Joe"
        }
    }
}
```

The following is a sample request payload for a DynamoDB read item operation:

```json
{
    "operation": "read",
    "tableName": "lambda-apigateway",
    "payload": {
        "Key": {
            "id": "1"
        }
    }
}
```

# **Setup**

# **Create Custom Policy**
We need to create a custom policy for least privilege

1. Open policies page in the IAM console
2. Click "Create policy" on top right corner
3. In the policy editor, click JSON, and paste the following

![Diagram 2](images/Lambda_custom_policy.png)

``` json

{
    "Version": "2012-10-17",
    "Statement": [
    {
      "Sid": "Stmt1428341300017",
      "Action": [
        "dynamodb:DeleteItem",
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:Query",
        "dynamodb:Scan",
        "dynamodb:UpdateItem"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "",
      "Resource": "*",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Effect": "Allow"
    }
    ]
    }
```

4. Give name "lambda-custom-policy", and click "Create policy" on botom right

# **Create Lambda IAM Role**

Create the execution role that gives your function permission to access AWS resources.

To create an execution role

1. Open the roles page in the IAM console.

2. Choose Create role.

3. Create a role with the following properties.

Trusted entity type – AWS service, then select Lambda from Use case

Permissions – In the Permissions policies page, in the search bar, type lambda-custom-policy. The newly created policy should show up. Select it, and click Next.

![Diagram 3](images/Lambda_custom_role.png)

Role name – lambda-apigateway-role.

Click "Create role"


