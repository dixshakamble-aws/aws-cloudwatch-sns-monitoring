# aws-cloudwatch-sns-monitoring
 Real-time AWS CloudWatch + SNS alerting walkthrough with troubleshooting notes and screenshots.

# Overview
This mini‑project demonstrates how to build a real‑time alerting pipeline on AWS using Amazon CloudWatch and Amazon SNS.
A CloudWatch alarm monitors an EC2 instance metric and sends notifications to an SNS topic, which then delivers emails to a subscribed address.

# Project structure
- The repository root contains the main `README.md` file, which provides a high-level overview of the project.
- The `docs` directory contains a detailed walkthrough in `docs/README.md`, including step-by-step instructions and explanations.
- The `images` directory stores all AWS console screenshots, which are referenced from the documentation in `docs/README.md`.

# Technologies used
AWS CloudWatch – metrics and alarms for EC2.

AWS SNS (Simple Notification Service) – topic and email subscription for alerts.

Amazon EC2 – instance generating CPU metrics for the alarm.

GitHub – documentation and versioning of the project.

# Features
End‑to‑end CloudWatch → SNS → Email alert pipeline.

Screenshot‑driven walkthrough of each configuration step in the AWS console.

Notes on common issues (like Insufficient data state) and how to troubleshoot them.

Clean‑up steps to safely remove alarms, SNS topics, subscriptions, and EC2 instances.

# Acknowledgments
AWS official documentation for CloudWatch alarms, SNS topics, and EC2 basics.​

Various community tutorials and Q&A threads that helped clarify alarm state behavior and SNS email delivery issues.​
