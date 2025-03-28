### adding ece2 logs to cloudwatch loggroups 

```txt
0-create a IAM role to allow ec2 send logs to log stream

1-create cloudwatch log groups

2-install aws log agent in ec2 instance 

3-configure aws agent config file 

```
### Step 0: Create a IAM role
 ```bash
 AWS Console → IAM → Create role → AWS Service → EC2 → CloudWatchAgentServerPolicy → Name & Create the Role → EC2-CloudWatch-Logs-Role


Go to EC2 Dashboard → Instances.

Select your EC2 instance.

Click Actions → Security → Modify IAM Role.

Select the IAM Role (EC2-CloudWatch-Logs-Role).

Click Update IAM Role.


 ```

### Step 1: Download the CloudWatch Agent Package

```bash
cd /tmp
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
 
 ```
 ### Step 2: Install the CloudWatch Agent
 ```bash
  dpkg -i amazon-cloudwatch-agent.deb
  ```

  ### Step3:Modify the CloudWatch Agent Configuration
  ```bash
 sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

  ```
  ```json
  {
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/syslog",
            "log_group_name": "EC2-Logs",
            "log_stream_name": "{instance_id}/syslog",
            "timezone": "UTC"
          },
          {
            "file_path": "/var/log/auth.log",
            "log_group_name": "EC2-Logs",
            "log_stream_name": "{instance_id}/authlog",
            "timezone": "UTC"
          }
        ]
      }
    }
  }
}

```
```bash
sudo systemctl enable amazon-cloudwatch-agent
sudo systemctl start amazon-cloudwatch-agent
```
