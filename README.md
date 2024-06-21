# Welcome to your CDK TypeScript project

This is a test project for CDK development with TypeScript.

The `cdk.json` file tells the CDK Toolkit how to execute your app.

## Useful commands

- `npm run build` compile typescript to js
- `npm run watch` watch for changes and compile
- `npm run test` perform the jest unit tests
- `npx cdk deploy` deploy this stack to your default AWS account/region
- `npx cdk diff` compare deployed stack with current state
- `npx cdk synth` emits the synthesized CloudFormation template

## How to hello world!

### Prerequisites

- Create an AWS account
- Install have the AWS Command Line Interface (AWS CLI) and configure with the account.
- Install Node.js and npm.
- Install the CDK Toolkit globally, using npm install -g aws-cdk.

### Step 1: Create a CDK project

```bash
mkdir cdk-hello-world && cd cdk-hello-world # keep this directory empty
cdk init --language typescript
npm install aws-cdk-lib constructs
```

### Step 2: Create your Lambda function

```bash
mkdir lambda && cd lambda
touch hello.js
```

```javascript
// lambda/hello.js
exports.handler = async (event) => {
  return {
    statusCode: 200,
    headers: { "Content-Type": "text/plain" },
    body: JSON.stringify({ message: "Hello, World!" }),
  };
};
```

### Step 3: Define your constructs

```typescript
// lib/cdk-hello-world-stack.ts
import * as cdk from "aws-cdk-lib";
import { Construct } from "constructs";
// Import Lambda L2 construct
import * as lambda from "aws-cdk-lib/aws-lambda";
//Import API Gateway L2 construct
import * as apigateway from "aws-cdk-lib/aws-apigateway";

export class CdkHelloWorldStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // Define the Lambda function resource
    const helloWorldFunction = new lambda.Function(this, "HelloWorldFunction", {
      runtime: lambda.Runtime.NODEJS_20_X, // Choose any supported Node.js runtime
      code: lambda.Code.fromAsset("lambda"), // Points to the lambda directory
      handler: "hello.handler", // Points to the 'hello' file in the lambda directory
    });

    // Define the API Gateway resource
    const api = new apigateway.LambdaRestApi(this, "HelloWorldApi", {
      handler: helloWorldFunction,
      proxy: false,
    });

    // Define the '/hello' resource with a GET method
    const helloResource = api.root.addResource("hello");
    helloResource.addMethod("GET");
  }
}
```

### Step 4: Prepare your application for deployment

```bash
npm run build
cdk synth # this makes AWS CloudFormation template.
```

### Step 5: Deploy your application

```bash
# If bootstrap error occurs and it says "run cdk bootstrap", Run it and retry.
cdk deploy
#cdk bootstrap; cdk deploy
```

### Step 6: Interact with your application

```bash
curl https://<api-id>.execute-api.<region>.amazonaws.com/prod/hello
{"message":"Hello World!"}%
```

### Step 7: Delete your application

```bash
cdk destroy
```

---

If you want further tutorial, read this [reference doc](https://docs.aws.amazon.com/cdk/v2/guide/serverless_example.html).
