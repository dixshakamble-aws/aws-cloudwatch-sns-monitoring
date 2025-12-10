# AWS CloudWatch + SNS Monitoring – Step-by-Step

This document walks through configuring real-time alerts from Amazon CloudWatch to Amazon SNS, and cleaning up all resources afterward. It is based on a simple EC2 CPUUtilization alarm that sends email notifications.

## Step 1 – Create SNS topic and email subscription

- Open **Amazon SNS** in the AWS Management Console.
- Create a **Standard** topic named `cloudwatch-alerts-topic` to receive CloudWatch alarm messages.
- From the topic page, create an **Email** subscription with your email address.
- Open your inbox and confirm the subscription so its status changes to **Confirmed**.

![SNS topic](images/topic.png)
![SNS email subscription](images/subcription-details.png)

## Step 2 – Launch an EC2 instance for metrics

- Open **Amazon EC2** in the console and click **Launch instance**.
- Choose an **Amazon Linux** AMI and a small free-tier instance type such as `t2.micro` or `t3.micro`.
- Use the default VPC and subnet, and create or select a security group that at least allows **SSH from your IP**.
- Launch the instance and wait until its state becomes **running**.

![EC2 instance running](images/cw-ec2.png)

## Step 3 – Create a CloudWatch alarm on EC2 CPU

- Open **Amazon CloudWatch** and go to **Alarms → All alarms → Create alarm**.
- Click **Select metric**, then choose **EC2 → Per-Instance Metrics**.
- Select the **CPUUtilization** metric for the EC2 instance you just launched.
- Set the statistic to **Average** and the period to something like **5 minutes**.
- Use a threshold such as “CPUUtilization ≥ 70% for 1 datapoint within 5 minutes”.

![Choose CPU metric](images/cw2.png)
![Set alarm conditions](images/cw3.png)

## Step 4 – Connect the alarm to the SNS topic

- In the **Configure actions** step of the alarm wizard, set the **Alarm state trigger** to **In alarm**.
- For **Send a notification to**, choose your existing SNS topic `cloudwatch-alerts-topic`.
- Continue to the **Add name and description** step, give the alarm a clear name like `High-CPU-EC2-Alarm`, then create or update the alarm.

![Alarm actions to SNS](images/cw4.png)
![Alarm summary](images/cw5.png) 
![Alarm summary](images/cw6.png) 

## Step 5 – Generate / test an alarm

- Wait until the alarm leaves the initial **Insufficient data** state and shows **OK** (this may take a few evaluation periods).
- To trigger it for real, connect to the EC2 instance and run a CPU-heavy command for several minutes, or temporarily lower the threshold so it easily breaches.
- When the alarm enters the **ALARM** state, CloudWatch publishes a message to SNS and SNS sends an email to your subscribed address.

![Alarm in ALARM state](images/cw7.png)
![Received email notification](images/confirmation.png)

## Step 6 – Troubleshooting notes

- If the alarm stays in **Insufficient data**, the metric might not have enough datapoints yet, or the EC2 instance is stopped.
- If SNS shows the subscription as **Pending confirmation**, check your Spam/Promotions folders and click the confirmation link.
- If SNS test messages arrive but alarm emails do not, verify in the alarm details that the **In alarm** action points to the correct SNS topic and that actions are enabled.

![confirm Instance](images/cw8.png)
![Alarm details and mail](images/cw9.png)

## Step 7 – Cleaning up resources

To avoid ongoing costs:

- In **CloudWatch**, delete the `High-CPU-EC2-Alarm`.
- In **EC2**, terminate the demo instance you created for this project.
- In **SNS**, delete the email subscription and then delete the `cloudwatch-alerts-topic`.


 ## Issues encountered and resolutions

- Initially, no email arrived even though the CloudWatch alarm and SNS topic were created.
- To verify if SNS was working, a direct test message was published from the SNS topic, which successfully arrived in the inbox. This confirmed that email delivery and the subscription were fine.
- The real issue was that the alarm was using the wrong or missing EC2 InstanceId, so CloudWatch stayed in the `Insufficient data` state and never triggered.
- After updating the alarm to use the correct EC2 instance metric (CPUUtilization for the new instance), the alarm evaluated correctly and emails were received when the alarm went into the `ALARM` state.

![SNS publish message screen](images/cw-frcd-msg.png)
![SNS test message content](images/cw-forced-msg-txt.png)
![SNS confirmation email](images/cw-frcd-msg-cnfrm-mail.png)
