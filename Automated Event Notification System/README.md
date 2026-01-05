# Automated Event Notification System on AWS

## Overview

This project demonstrates how to build an automated event notification system on AWS using services like Amazon EventBridge and Amazon SNS. The system is designed to automatically send notifications (e.g., via email) in response to specific events occurring within your AWS environment. This example focuses on sending an email when a new object is created in an S3 bucket.

```mermaid
graph LR
    User((User)) -- "1. Uploads Object" --> S3[Amazon S3 Bucket]
    S3 -- "2. Emits Event: Object Created" --> EB{Amazon EventBridge}
    EB -- "3. Matches Rule" --> SNS[Amazon SNS Topic]
    SNS -- "4. Sends Email" --> Sub((Subscriber))
    
    classDef aws fill:#FF9900,stroke:#232F3E,stroke-width:2px,color:white;
    classDef other fill:#fff,stroke:#333,stroke-width:2px;
    
    class S3,EB,SNS aws;
    class User,Sub other;

## Goal

The goal of this project is to create an automated system that sends an email notification whenever a new object is uploaded to a designated Amazon S3 bucket.

## Prerequisites

* An AWS Account.
* Access to the AWS Management Console.
* A valid email address that you can access to confirm the SNS subscription.
* Basic understanding of AWS services (S3, EventBridge, SNS).

## Step-by-Step Deployment Guide (Using AWS Management Console)

1.  **Create an SNS Topic:**
    * Navigate to the SNS service in the AWS Console.
    * Create a new "Standard" topic with a descriptive name (e.g., `s3-object-creation-notification`).
    * Note the ARN (Amazon Resource Name) of the topic.

2.  **Subscribe to the SNS Topic:**
    * In your SNS topic, create a new subscription.
    * Choose "Email" as the protocol.
    * Enter the email address where you want to receive notifications as the endpoint.
    * **Confirm the subscription by clicking the link in the confirmation email sent by AWS.**

3.  **Create an S3 Bucket (if you don't have one):**
    * Navigate to the S3 service in the AWS Console.
    * Create a new S3 bucket with a globally unique name in the same AWS Region as your SNS topic.

4.  **Create an EventBridge Rule to Trigger on S3 Object Creation:**
    * Navigate to the EventBridge service in the AWS Console.
    * Create a new rule with a descriptive name (e.g., `s3-object-created-trigger`).
    * Select the default event bus.
    * Choose "Rule with an event pattern".
    * For "Event source", select "AWS services".
    * Choose "S3" as the AWS service and "Object Created" as the event type.
    * Under "Specific bucket(s)", select the name of your S3 bucket.
    * Click "Next".
    * For "Select target(s)", choose "SNS topic".
    * In the "Topic" dropdown, select the SNS topic you created earlier.
    * For "IAM role", it's recommended to select "Create a new role for this specific resource".
    * Click "Next" through the optional steps (Tags) and then "Create rule".

## Testing the System

1.  Navigate to your S3 bucket in the AWS Console.
2.  Upload any file to the bucket.
3.  Check the inbox of the email address you subscribed to the SNS topic. You should receive an email notification indicating that an object was created in your S3 bucket.

## (Optional) Configuration

* **Different Event Sources:** You can modify the EventBridge rule to trigger notifications for other AWS service events (e.g., EC2 instance state changes, CloudWatch alarm state changes).
* **Different Notification Targets:** Instead of SNS email, you could configure EventBridge to send events to other targets like AWS Lambda functions (for more complex processing), SQS queues, or even third-party services.
* **Filtering Events:** Within the EventBridge rule's event pattern, you can add more specific criteria to filter which S3 object creation events trigger the notification (e.g., based on a specific prefix or file type).
* **Customizing Email Content:** For more customized email notifications, you could configure EventBridge to trigger an AWS Lambda function, which could then format and send the email using SNS or another email service.

## (Optional) Extending the System

* **Adding More Event Types:** Create additional EventBridge rules to monitor and send notifications for other critical events in your AWS environment.
* **Integrating with Chat Platforms:** You could configure EventBridge to trigger a Lambda function that sends notifications to chat platforms like Slack or Microsoft Teams.
* **Implementing Remediation Actions:** Instead of just sending notifications, you could use EventBridge to trigger automated remediation actions in response to certain events.

## Cleanup

To avoid incurring unnecessary charges, remember to delete the following resources when you are finished experimenting:

1.  **Delete the EventBridge Rule** in the EventBridge console.
2.  **Delete the SNS Subscription** and then the **SNS Topic** in the SNS console.
3.  **Delete the S3 Bucket** (make sure it's empty first) in the S3 console.
