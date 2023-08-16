# Go Lambda Function Example

This sample project demonstrates how to develop AWS Lambda functions using the Go programming language. The project provides an example of building, packaging, and deploying a Go-based Lambda function.

## Prerequisites

Before you begin, ensure you have the following in place:

- [Go](https://golang.org/dl/) installed on your machine
- [AWS Command Line Interface (CLI)](https://aws.amazon.com/cli/) installed and configured

## Getting Started

### Step 1: Initialize the Go Module

```shell
go mod init github.com/theafwan/go-lambda-function
```

### Step 2: Create an IAM Role

Use the AWS CLI to create an IAM role with the necessary permissions for your Lambda function. Replace YOUR_AWS_ID with your AWS account ID.

```shell
aws iam --region us-east-1 create-role --role-name lambda-ex --assume-role-policy-document file://trust-policy.json
```

The trust-policy.json file contains the necessary trust policy configuration.

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Principal": {
				"Service": "lambda.amazonaws.com"
			},
			"Action": "sts:AssumeRole"
		}
	]
}
```

### Step 3: Tidy Go Modules

Ensure your Go module dependencies are properly managed.

```shell
go mod tidy
```

### Step 4: Build and Package the Lambda Function

#### For developers on Linux and macOS

```shell
# Remember to build your handler executable for Linux!
# When using the `provided.al2` runtime, the handler executable should be named `main`
GOOS=linux GOARCH=amd64 go build -o main main.go
zip lambda-handler.zip main
```

#### For developers on Windows

Windows developers may have trouble producing a zip file that marks the binary as executable on Linux. To create a .zip that will work on AWS Lambda, the `build-lambda-zip` tool may be helpful.

Get the tool

```shell
go.exe install github.com/aws/aws-lambda-go/cmd/build-lambda-zip@latest
```

Use the tool from your `GOPATH`. If you have a default installation of Go, the tool will be in `%USERPROFILE%\Go\bin`.

in cmd.exe:

```bat
set GOOS=linux
set GOARCH=amd64
set CGO_ENABLED=0
go build -o main main.go
%USERPROFILE%\Go\bin\build-lambda-zip.exe -o lambda-handler.zip main
```

in Powershell:

```posh
$env:GOOS = "linux"
$env:GOARCH = "amd64"
$env:CGO_ENABLED = "0"
go build -o main main.go
~\Go\Bin\build-lambda-zip.exe -o lambda-handler.zip main
```

### Step 5: Deploy the Lambda Function

Replace YOUR_AWS_ID with your AWS account ID.

```shell
aws lambda create-function --function-name go-lambda-function --zip-file fileb://lambda-handler.zip --role arn:aws:iam::YOUR_AWS_ID:role/lambda-ex --handler main --runtime go1.x
```

### Step 6: Invoke the Lambda Function

Invoke the deployed Lambda function using the AWS CLI.

```shell
aws lambda invoke --function-name go-lambda-function --cli-binary-format raw-in-base64-out --payload '{"What is your name?": "Afwan", "How old are you?": 28}' output.txt
```
