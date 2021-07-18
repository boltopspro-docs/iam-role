<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/iam-role/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# IAM Role CloudFormation Blueprint

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

This blueprint creates a single IAM Role.

## Usage

1. Add blueprint to Gemfile
2. Configure: configs/iam-role values
3. Deploy

## Add

Add the blueprint to your lono project's `Gemfile`.

```ruby
gem "iam-role", git: "git@github.com:boltopspro/iam-role.git"
```

## Configure

First you want to configure the [configs](https://lono.cloud/docs/core/configs/) files. Use [lono seed](https://lono.cloud/reference/lono-seed/) to configure starter values quickly.

    LONO_ENV=development lono seed iam-role

To deploy to additional environments:

    LONO_ENV=production  lono seed iam-role

The generated files in `config/iam-role` folder look something like this:

    configs/iam-role/
    ├── params
    │   ├── development.txt
    │   └── production.txt
    └── variables
        ├── development.rb
        └── production.rb

These files will contain placeholder data so that you can see which values you can modify to configure the stack. The development files will look something like this:

configs/iam-role/params/development.txt:

    # Parameter Group: AWS::IAM::Role
    # Description=
    # MaxSessionDuration=
    # Path=
    # PermissionsBoundary=
    # RoleName=

configs/iam-role/variables/development.rb:

```ruby
@iam_role_json = <<~JSON
# ... Paste IAM role json here
JSON

@assumed_role_policy_document = {
  Version: "2012-10-17",
  Statement: [
    {
      Effect: "Allow",
      Principal: {
        Service: "ec2.amazonaws.com"
      },
      Action: "sts:AssumeRole"
    }
  ]
}

@managed_policy_arns = ["arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"]

@policies = [
  {
    PolicyName: "logs",
    PolicyDocument: {
      Version: "2012-10-17",
      Statement: [
        {
          Effect: "Allow",
          Action: [
            "logs:CreateLogGroup",
            "logs:CreateLogStream",
            "logs:PutLogEvents",
            "logs:DescribeLogStreams"
          ],
          Resource: "arn:aws:logs:*:*:*"
        }
      ]
    }
  }
]

@tags = {Name: sub("${AWS::StackName}")}
```

## Deploy

Use the [lono cfn deploy](http://lono.cloud/reference/lono-cfn-deploy/) command to deploy. Example:

    LONO_ENV=development lono cfn deploy iam-role-development --blueprint iam-role --sure
    LONO_ENV=production  lono cfn deploy iam-role-production  --blueprint iam-role --sure

## Configure: More Details

### Stack Name Convention

By leveraging the lono Stack Name and [CLI conventions](https://lono.cloud/docs/conventions/cli/), we can organize the configs files in a way that matches the stack name. Example:

    lono cfn deploy developer   --blueprint iam-role
    lono cfn deploy image-server --blueprint iam-role

Will use the corresponding config files:

    configs/iam-role/development/developer.txt
    configs/iam-role/development/image-server.txt


## IAM Permissions

The IAM permissions required for this stack are described below.

Service | Description
--- | ---
cloudformation | To launch the CloudFormation stack.
