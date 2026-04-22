
# Automated Log Collection using AWS CloudWatch

Collect logs from Amazon EC2 instances into Amazon CloudWatch and automate log collection based on schedule (10 AM–7 PM, Mon–Fri) with proper security and log management.

---

### Architecture Overview

- Logs generated in EC2
- CloudWatch Agent get the logs
- Amazon EventBridge triggers schedule
- AWS Systems Manager executes commands
- IAM roles control access

<img width="982" height="701" alt="Automation_Log_Collection" src="https://github.com/user-attachments/assets/f45b43ae-6e23-41e6-9d86-26ffec4347f7" />


---

### WorkFlow

- EC2 generates logs (/var/log/syslog)
- CloudWatch Agent collects logs
- EventBridge triggers at scheduled time i.e. from 10AM to 7PM, Mon–Fri
- Systems Manager runs command on EC2 i.e. start/stop the CloudWatch Agent
- Logs are pushed to CloudWatch
- Logs are also rotated in the server, to reduce disk usage

---

### Implementation Details

#### 1. EC2 Setup
- Create instance
- Attach IAM role
- Install and configure AWS CLI 
- Configure SSM Agent. After configuring it, you should be able to see your instance in it.

#### 2. CloudWatch Agent Configuration
- Install and configure the Agent for Logs
- Define your logs path there like (/var/log/syslog)
- Create log group
- Enabled Retention period of 7 days

#### 3. Automation Setup
- Start schedule at 10AM on weekdays
- Define your action there i.e. start CloudWatch Agent
- Stop schedule at 7PM on weekdays
- Define your action there i.e. stop CloudWatch Agent

#### 4. Log Rotation
- Configured it using logrotate
- Rotation: daily
- Retention: 7 days
- Compression enabled
- Used copytruncate for active logs

---

## Security

- Used IAM roles instead of access keys
- EC2 role has CloudWatchAgentServerPolicy and AmazonSSMManagedInstanceCore
- EventBridge role has only (ssm:SendCommand) permission
- No hardcoded credentials
- Followed least privilege principle

---

## Validation

- Logs are visible in CloudWatch
- Agent Start/Stop via SSM
- EventBridge schedules the execution
- Log rotation verified manually
- Logs continue after rotation

---

## Challenges Faced

- Wrong SSM Agent package, old one instead of snap
- Didn't add this (AmazonSSMManagedInstanceCore) permission to IAM Role, so SSM is not registeringthe instance
- EventBridge role confusion
- Syntax error in logrotate config file

