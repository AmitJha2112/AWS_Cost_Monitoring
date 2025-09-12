# AWS Cost Monitoring with Slack Integration
This project helps monitor AWS costs using the aws-cost-cli and automatically sends cost reports to a Slack channel.

## 🚀 Features
- AWS cost breakdown by service
- Cost summary (daily/weekly/monthly)
- Slack integration (send reports directly to a channel)
- JSON/Plaintext output for automatio
- Can be automated with CI/CD

### Setup EC2 Instance
Provision an EC2 t2.micro instance with 15GB storage (Ubuntu 22.04 recommended).

SSH into the server:
```
ssh -i your-key.pem ubuntu@<EC2_PUBLIC_IP>
```
Update system:
```
sudo apt update && sudo apt upgrade -y
```

### Install Node.js (via NVM)
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
source ~/.nvm/nvm.sh
nvm install 22
node -v
npm -v
```

### Install AWS CLI
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

Configure AWS credentials:
```
aws configure
```

### Install aws-cost-cli

Install globally:
```
npm install -g aws-cost-cli
```

### Usage Examples
🔹 Cost Breakdown by Service
```
aws-cost
```
<img width="1084" height="498" alt="Screenshot from 2025-08-22 12-01-35" src="https://github.com/user-attachments/assets/87376bc3-1a88-4f73-a3e4-c7ab643acc97" />

🔹 Cost Summary (No Breakdown)
```
aws-cost --summary
```
<img width="737" height="250" alt="Screenshot from 2025-08-22 12-02-15" src="https://github.com/user-attachments/assets/e0ceafe8-ac72-4618-aad0-f2b8192f0b8f" />

🔹 JSON Output (Automation)
```
aws-cost --json
```

🔹 Send Report to Slack
```
aws-cost --slack-token <SLACK_TOKEN> --slack-channel <CHANNEL_ID>
```
### Slack Setup

- Go to Slack API: Create App

- Create a new app → From Scratch

- Add Bot Token Scopes under OAuth & Permissions:

  - chat:write (send messages)

  - chat:write.public 

  - files:write (upload reports)

- Install the app → Copy Bot User OAuth Token (starts with xoxb-...)

- Get your Channel ID from Slack channel settings.

### Python Slack Upload Script

If you want to upload reports as files:

Install dependencies
```
sudo apt install -y python3 python3-pip python3-venv
python3 -m venv venv
source venv/bin/activate
pip install slack-sdk
```

Script: `upload_cost_report.py`
```py
from slack_sdk import WebClient
from slack_sdk.errors import SlackApiError

slack_token = "xoxb-your-slack-token"
client = WebClient(token=slack_token)

try:
    response = client.files_upload_v2(
        channel="CXXXXXXXX",
        initial_comment="AWS Cost Report",
        file="cost-report.txt"
    )
    print("File uploaded successfully:", response)
except SlackApiError as e:
    print(f"Error uploading file: {e.response['error']}")
```

Run it
```
aws-cost --text > cost-report.txt
python3 upload_cost_report.py
```

### CI/CD Automation

You can add this to a cronjob or CI/CD pipeline to automate daily reports.

Example cronjob (daily at 9AM):

```
crontab -e
0 9 * * * aws-cost --text > /home/ubuntu/cost-report.txt && python3 /home/ubuntu/upload_cost_report.py
```

<img width="1920" height="958" alt="image" src="https://github.com/user-attachments/assets/07364e97-3295-42ba-9abd-a71b777ecc92" />


👉 With this, you’ll have AWS costs automatically reported to Slack 🎯





