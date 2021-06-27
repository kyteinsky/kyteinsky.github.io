+++
author = "Anupam Kumar"
title = "Installing Terraform and Creating an EC2 Instance"
date = "2021-06-27"
description = ""
tags = [
    "terraform",
    "ec2",
    "instance",
]
categories = [
    "daily-progress",
]
series = ["Terraform", "AWS"]
asciinema = true
image = 'terraform-logo-extended.jpg'
+++

&nbsp;

## Ensure the prerequisites
* An AWS Account
* AWS cli installed, instructions for the same can be found [here](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).
* Have generated credentials (access key and secret), can be created from [here](https://console.aws.amazon.com/iam/home?#/security_credentials).

## Setting up AWS CLI
Type `aws configure` on the terminal and enter the Access Key ID and Secret Access Key, other fields can be blank.

## Creating a config file for terraform to understand
* Create a directory and switch to it
* Create a file named main.tf and populate it with the following sample configuration, 
where 
  - `app_server` is the name of the instance
  - `us-west-2` is the region where the instance will be deployed
  - `ami-830c94e3` is the unique identifier for the instance
  - `t2.micro` is the instance type
  - `ExampleAppServerInstance` is the tag name for grouping of instances

```tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.27"
    }
  }

  required_version = ">= 0.14.9"
}

provider "aws" {
  profile = "default"
  region  = "us-west-2"
}

resource "aws_instance" "app_server" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```

## Installation of and Working with Terraform CLI:
* Terraform can be installed following [this guide](https://learn.hashicorp.com/tutorials/terraform/install-cli)
* Configuration of terraform CLI can be done using command `terraform init`
* The configuration file can be formatted using `terraform fmt` command
* And the file can be validated using `terraform validate` command
* Finally all the changes can be viewed using `terraform plan` command and perfomed using `terraform apply` command


## Here is a terminal cast performing the above actions
{{< asciinema key="terraform/terraform-1.cast" rows=35 preload="1" >}}
