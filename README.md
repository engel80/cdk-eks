# CDK ECS Sample

## Prequisets

```bash
npm install -g aws-cdk@2.27.0

# install packages in the root folder
npm install
cdk bootstrap
```

Use the `cdk` command-line toolkit to interact with your project:

* `cdk deploy`: deploys your app into an AWS account
* `cdk synth`: synthesizes an AWS CloudFormation template for your app
* `cdk diff`: compares your app with the deployed stack
* `cdk watch`: deployment every time a file change is detected

## CDK Stack

| Stack                         | Time    |
|-------------------------------|---------|
| VPC                           | 4m      |
| EKS cluster                   | 13m     |
| EKS nodegroups                | 10m     |
| Deploy(including ALB)         | 4m      |
| Total                         | 31m     |

## Install

### Step 1: VPC

The VPC ID will be saved into the SSM Parameter Store to refer from other stacks.

Parameter Name: `/cdk-eks/vpc-id`

Use the `-c vpcId` context parameter to use the existing VPC.

```bash
cd vpc
cdk deploy
```

[vpc/lib/vpc-stack.ts](./vpc/lib/vpc-stack.ts)

### Step 2: EKS cluster

```bash
cd ../eks-cluster-nodegroup
cdk deploy 

# or define your VPC id with context parameter
cdk deploy -c vpcId=<vpc-id>
```

[eks-cluster/lib/cluster-stack.ts](./eks-cluster/lib/cluster-stack.ts)

SSM parameter:

* /cdk-eks/vpc-id

Cluster Name: [cluster-config.ts](./cluster-config.ts)

### Step 3: EKS nodegroup

```bash
cd ../eks-nodegroup
cdk deploy 
```

SSM parameters:

* /cdk-eks/vpc-id
* /${clusterName}/openid-connect-provider-arn
* /${clusterName}/kubectl-role-arn

```bash
clusterName: eks-cluster-local, eks-cluster-dev, eks-cluster-stg
```

[eks-nodegroup/lib/nodegroup-stack.ts](./eks-nodegroup/lib/nodegroup-stack.ts)

### Step 4: Deploy

```bash
TBD
```

## Uninstall

```bash
find . -name "cdk.context.json" -exec rm -f {} \;

cd eks-nodegroup
cdk destroy

cd ../eks-cluster
cdk deploy

cd ../vpc
cdk deploy
```

## Structure

```text
├── build.gradle
├── package.json
├── ssm-prefix.ts
├── tsconfig.json
├── vpc
│   ├── bin
│   │   └── index.ts
│   ├── cdk.json
│   └── lib
│       └── vpc-stack.ts
├── eks-cluster
│   ├── bin
│   │   └── index.ts
│   ├── cdk.json
│   ├── lib
│   │   ├── cluster-config.ts
│   │   └── cluster-stack.ts
│   └── settings.yaml
├── app
│   ├── Dockerfile
│   ├── README.md
│   ├── build.sh
│   ├── flask_api.py
│   ├── gunicorn.config.py
│   └── requirements.txt
```

## Reference

TBD