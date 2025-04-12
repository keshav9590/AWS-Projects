# Serverless Static Website Hosting with Global Distribution and Monitoring on AWS

## Overview

This project demonstrates how to host a simple static website securely and cost-effectively using AWS services. It leverages Amazon S3 for storage, Amazon CloudFront for global content delivery and caching, and Amazon CloudWatch for basic error monitoring. This setup provides high availability, low latency, and automatic scaling for your static website.

## Goal

The goal of this project is to deploy a simple HTML page securely (HTTPS) using S3 for storage and CloudFront for global delivery and caching, with basic error monitoring.

## Prerequisites

* An AWS Account.
* Access to the AWS Management Console.
* Basic understanding of AWS services (S3, CloudFront, CloudWatch).
* A text editor to create the HTML files.

## Step-by-Step Deployment Guide (Using AWS Management Console)

1.  **Prepare Your Simple Website Files:**
    * Create an `index.html` file with your website's content.
    * (Optional but Recommended) Create an `error.html` file for error pages.

2.  **Create an S3 Bucket:**
    * Navigate to the S3 service in the AWS Console.
    * Click "Create bucket" and choose a globally unique name.
    * Select an AWS Region.
    * Leave "ACLs disabled" selected.
    * **Crucially, uncheck "Block all public access"** and acknowledge the warning.
    * Click "Create bucket".

3.  **Enable Static Website Hosting on the Bucket:**
    * Go to your bucket's "Properties" tab.
    * Scroll down to "Static website hosting" and click "Edit".
    * Select "Enable" hosting type.
    * Set "Index document" to `index.html`.
    * (Optional) Set "Error document" to `error.html`.
    * Click "Save changes".
    * **Note down the "Bucket website endpoint URL".**

4.  **Set Bucket Policy for Public Read Access:**
    * Go to your bucket's "Permissions" tab.
    * Click "Edit" in the "Bucket policy" section.
    * Paste the following policy, **replacing `YOUR-BUCKET-NAME` with your actual bucket name**:
        ```json
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "PublicReadGetObject",
                    "Effect": "Allow",
                    "Principal": "*",
                    "Action": "s3:GetObject",
                    "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
                }
            ]
        }
        ```
    * Click "Save changes".

5.  **Upload Your Website Files to S3:**
    * Go to your bucket's "Objects" tab.
    * Click "Upload" and add your `index.html` and `error.html` files.
    * Click "Upload".

6.  **(Optional) Test S3 Website Endpoint:**
    * Go to your bucket's "Properties" tab and click the "Bucket website endpoint URL" to verify your website loads over HTTP.

7.  **Create a CloudFront Distribution:**
    * Navigate to the CloudFront service in the AWS Console.
    * Click "Create distribution".
    * For "Origin domain", **paste the Bucket website endpoint URL** you noted down (without `http://`).
    * Set "Protocol" to "HTTP only".
    * Set "Viewer protocol policy" to "Redirect HTTP to HTTPS".
    * Set "Allowed HTTP methods" to "GET, HEAD, OPTIONS".
    * Keep the default "Cache policy and origin request policy (recommended)" with the "CachingOptimized" cache policy.
    * For "Web Application Firewall (WAF)", select "Do not enable security protections".
    * Keep the default "Price class".
    * Set "Default root object" to `index.html`.
    * Click "Create distribution".
    * **Wait for the distribution status to become "Enabled".**

8.  **Test Your Website via CloudFront:**
    * Once deployed, copy the "Distribution domain name" from the CloudFront console.
    * Paste it into your browser to access your website securely over HTTPS.

9.  **Set Up CloudWatch Monitoring Alarm:**
    * Navigate to the CloudWatch service.
    * Go to "Alarms" and click "Create alarm".
    * Select the "5xxErrorRate" metric under "CloudFront" -> "Per-Distribution Metrics" for your distribution.
    * Configure the alarm to trigger when the average error rate is greater than 1% over 5 minutes.
    * Create a new SNS topic to receive email notifications for the alarm.
    * **Confirm your email subscription when you receive the confirmation email.**
    * Give your alarm a descriptive name and create it.

## Cleanup

To avoid incurring unnecessary costs, remember to delete the following resources when you are finished:

1.  **CloudFront Distribution:** Disable and then delete your CloudFront distribution.
2.  **CloudWatch Alarm:** Delete the CloudWatch alarm you created.
3.  **SNS Topic and Subscription:** Delete the SNS topic and any associated subscriptions.
4.  **S3 Bucket:** Empty your S3 bucket and then delete the bucket itself.