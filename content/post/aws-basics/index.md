+++
author = "Anupam Kumar"
title = "AWS Console and CLI Exploration"
date = "2021-06-29"
description = "Visited AWS Console for knowing it better from NPBridge Organization and generated access keys for playing around with the CLI Tool."
tags = [
	"aws",
	"ec2",
	"s3",
  "vpc",
  "cli",
]
categories = [
	"daily-progress",
]
series = ["AWS"]
aliases = ["aws-basics"]
image = 'aws-logo-full.png'
asciinema = true
+++

&nbsp;

## Inside the AWS Console:

* Explored compute machine options
	* EC2 is the easiest of all, provides vanilla virtual machine experience for fully self-managing the infrastructure
	* Lambda is for small fuctions or API calls
	* App Runner provides a full-managed infrastructure to run our app handling all the scaling, load balancing, installation of software and deployment.
	* Elastic Beanstalk is a similar service to App Runner, but there is a slight difference, Elastic Beanstalk provides more control over infrastructure after deployment, whereas App Runner manages it fully even after deployment. [(source)](https://acloudguru.com/blog/engineering/what-you-need-to-know-about-aws-app-runner)

* Explored storage options
	* S3, Simple Storage Service, used for every purpose like storing files and also hosting static websites
	* EFS, Elastic File System, used to store files that can be attached to multiple EC2 instances, are fully-managed and scale automatically
	* FSx is used for Windows Servers and playing nice with its ecosystem

* VPC
	* Used to create a private space in cloud for related, non-public services to live in
	* One or more services can be exposed to the internet and other VPCs or AWS services, while secure services like databases can be secured and only accessed by some hidden service inside the VPC making it more secure

## Creating two EC2 instances inside a VPC and one S3 bucket using AWS CLI and then clearing off all the resources (VPC deleted through Console because of security group being of type 'default' was not being deleted through CLI for some convoluted reason

{{< asciinema key="aws-basics/aws-basics.cast" rows=35 preload="1" >}}

&nbsp;

