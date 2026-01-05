# Resource Compliance Monitoring with AWS Config

## Overview

This project demonstrates how to use AWS Config to continuously monitor the configuration of your AWS resources and ensure they comply with defined rules. AWS Config provides a detailed view of the configuration of AWS resources in your account and how they have changed over time. This example focuses on setting up a rule to ensure all S3 buckets have server-side encryption enabled.

```mermaid
graph LR
    Resources[Target S3 Buckets] -- "1. Records Configuration" --> Config{AWS Config}
    Config -- "2. Stores History" --> HistBucket[Config History S3 Bucket]
    Config -- "3. Evaluates Rule" --> Rule[Managed Rule: Encryption Check]
    Rule -- "4. Reports Status" --> Dash((Compliance Dashboard))

    classDef aws fill:#FF9900,stroke:#232F3E,stroke-width:2px,color:white;
    classDef other fill:#fff,stroke:#333,stroke-width:2px;

    class Config,HistBucket,Rule aws;
    class Resources,Dash other;

## Goal

The goal of this project is to enable AWS Config, configure it to record AWS resource configurations, and implement a compliance rule to verify that all Amazon S3 buckets have server-side encryption enabled.

## Prerequisites

* An AWS Account.
* Access to the AWS Management Console.
* Basic understanding of AWS Config and Amazon S3.

## Step-by-Step Deployment Guide (Using AWS Management Console)

1.  **Enable AWS Config:**
    * Navigate to the AWS Config service in the AWS Console.
    * Choose the AWS Region where you want to monitor resources.
    * Click "Get Started" (if it's your first time).
    * Select "Record all resources in this region" (recommended) or choose specific resource types.
    * Include global resources (e.g., IAM).
    * Specify an S3 bucket for Config to deliver configuration history and snapshots (create a new one if needed, e.g., `aws-config-history-youraccountid-yourregion`).
    * Choose to create a new AWS Config service role (recommended) or select an existing one with the necessary permissions.
    * Click "Next" and then "Skip setup and go to Config dashboard".

2.  **Create an AWS Config Rule to Check for S3 Bucket Encryption:**
    * In the AWS Config console, navigate to "Rules".
    * Click "Add rule".
    * Choose "Use AWS managed rule".
    * Search for and select the rule `s3-bucket-server-side-encryption-enabled`.
    * Configure the rule name (optional).
    * Review the scope and parameters (usually defaults are fine).
    * Click "Next" and then "Add rule".

## Usage

1.  **View Compliance Status:**
    * In the AWS Config console, navigate to "Rules".
    * Find the `S3BucketEncryptionEnabled` rule. The "Compliance" column will show the status of your S3 buckets.
    * Click on the rule name to see a list of compliant and non-compliant S3 buckets.
    * Click on a "Resource ID" to view the configuration details of a specific bucket.

2.  **View Configuration History:**
    * In the AWS Config console, navigate to "Configuration timeline".
    * Filter by "Resource type" (e.g., `AWS::S3::Bucket`) and optionally by "Resource ID" to see the history of configuration changes for specific S3 buckets.
    * Click on an event in the timeline to view the detailed configuration at that point.

## (Optional) Extending the System

* **Explore More AWS Managed Rules:** AWS Config offers a wide variety of pre-built rules for various compliance checks (e.g., checking for public S3 buckets, unused security groups, etc.). Explore these in the "Add rule" section.
* **Create Custom Rules:** For more specific compliance requirements, you can create custom rules using AWS Lambda functions. This allows for highly tailored checks based on your organization's policies.
* **Configure Remediation Actions:** Some AWS Config rules support automated remediation. You can configure Config to automatically take actions to bring non-compliant resources back into compliance.
* **Integrate with Other Services:** You can integrate AWS Config with other services like AWS Security Hub for a centralized view of your security and compliance posture.

## Cleanup

To avoid incurring unnecessary charges, remember to clean up the resources you created:

1.  **Delete the AWS Config Rule** in the AWS Config console under "Rules".
2.  **(Optional) Disable AWS Config:** If you no longer need it, you can disable AWS Config in the "Settings" section of the AWS Config console.
3.  **Delete the S3 Bucket for Config Data** if you created a new one specifically for this project. Ensure the bucket is empty before deleting it.
