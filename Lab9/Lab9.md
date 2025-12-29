# 🧪 Lab 9 – Codespaces + AWS: Complete Documentation

**Student Name:** Arshia Jadoon  
**Roll Number:** BSE-2023-014  
**Lab Title:** GitHub CLI (Codespaces), AWS CLI, EC2, IAM, Security Groups, Filters & Queries

---

## 📋 Table of Contents

- [Overview](#overview)
- [Lab Objectives](#lab-objectives)
- [Prerequisites](#prerequisites)
- [Task 1: GitHub CLI & Codespace Setup](#task-1-github-cli--codespace-setup)
- [Task 2: AWS CLI Installation](#task-2-aws-cli-installation)
- [Task 3: Security Group Configuration](#task-3-security-group-configuration)
- [Task 4: EC2 Instance Management](#task-4-ec2-instance-management)
- [Task 5: AWS Resource Inspection](#task-5-aws-resource-inspection)
- [Task 6: IAM Management](#task-6-iam-management)
- [Task 7: Instance Filtering](#task-7-instance-filtering)
- [Task 8: Query Formatting](#task-8-query-formatting)
- [Cleanup](#cleanup)
- [Key Learnings](#key-learnings)
- [Troubleshooting](#troubleshooting)

---

## 🎯 Overview

This lab demonstrates comprehensive cloud infrastructure management using GitHub Codespaces and AWS CLI. All tasks were completed in a remote Linux environment (Codespace) to simulate real-world cloud operations.

**Duration:** 3 hours  
**Environment:** GitHub Codespace (Ubuntu Linux)  
**Cloud Provider:** AWS (Amazon Web Services)

---

## 🎓 Lab Objectives

By completing this lab, I learned to:

✅ Install and authenticate GitHub CLI for Codespaces  
✅ Create and connect to remote development environments  
✅ Install and configure AWS CLI in Linux  
✅ Manage EC2 security groups and ingress rules  
✅ Create SSH key pairs and launch EC2 instances  
✅ Connect to EC2 instances via SSH  
✅ Inspect AWS resources using describe commands  
✅ Create and manage IAM users, groups, and policies  
✅ Use AWS CLI filters and queries for data extraction  
✅ Format AWS outputs for reporting purposes  

---

## 📦 Prerequisites

- GitHub account with Codespaces access
- AWS account (Free Tier eligible)
- GitHub CLI installed locally
- Basic understanding of cloud computing concepts
- Familiarity with command-line interfaces

---

## 🚀 Task 1: GitHub CLI & Codespace Setup

### Objective
Install GitHub CLI, authenticate for Codespaces, create a Codespace, and establish SSH connection.

### Steps Completed

#### 1.1 GitHub CLI Installation
```powershell
winget install --id GitHub.cli
```
**Status:** ✅ Successfully installed

#### 1.2 GitHub Authentication
```bash
gh auth login -s codespace
```
**Token Scopes:**
- admin:org
- codespace
- repo

**Status:** ✅ Authenticated successfully

#### 1.3 Codespace Listing
```bash
gh codespace list
```
**Result:** Initially no codespaces found

#### 1.4 Codespace Creation & Connection
```bash
gh codespace create --repo arshiajadoon/LAB_9 --branch main
gh codespace ssh
```
**Status:** ✅ Connected to Codespace environment

### Screenshots
- `task1_gh_install.png` - GitHub CLI installation
- `task1_gh_auth_login.png` - Authentication process
- `task1_codespace_list.png` - Codespace listing
- `task1_codespace_ssh_connected.png` - SSH connection established

---

## ☁️ Task 2: AWS CLI Installation

### Objective
Install AWS CLI in the Codespace and configure credentials.

### Steps Completed

#### 2.1 AWS CLI Installation
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```
**Version Installed:** AWS CLI 2.x

#### 2.2 AWS Configuration
```bash
aws configure
```
**Configuration:**
- Access Key ID: [Configured]
- Secret Access Key: [Configured]
- Default region: me-central-1
- Output format: json

#### 2.3 Credential Verification
```bash
cat ~/.aws/credentials
cat ~/.aws/config
```

#### 2.4 Connection Test
```bash
aws sts get-caller-identity
```
**Status:** ✅ Successfully authenticated with AWS

### Screenshots
- `task2_aws_install_and_version.png` - Installation and version check
- `task2_aws_configure_and_files.png` - Configuration files
- `task2_aws_get_caller_identity.png` - Identity verification

---

## 🔒 Task 3: Security Group Configuration

### Objective
Create EC2 security group and configure ingress rules for SSH and HTTP.

### Steps Completed

#### 3.1 Security Group Creation
```bash
aws ec2 create-security-group \
  --group-name 'MySecurityGroup' \
  --description 'My Security Group' \
  --vpc-id 'vpc-[ID]'
```
**Security Group ID:** sg-[GENERATED_ID]

#### 3.2 Initial Inspection
```bash
aws ec2 describe-security-groups --group-ids sg-[ID]
```

#### 3.3 Codespace IP Retrieval
```bash
curl icanhazip.com
```
**Public IP:** [Retrieved successfully]

#### 3.4 SSH Rule (Port 22)
```bash
aws ec2 authorize-security-group-ingress \
  --group-id sg-[ID] \
  --protocol tcp \
  --port 22 \
  --cidr [IP]/32
```

#### 3.5 HTTP Rule (Port 80)
```bash
aws ec2 authorize-security-group-ingress \
  --group-id sg-[ID] \
  --ip-permissions '{"FromPort":80,"ToPort":80,"IpProtocol":"tcp","IpRanges":[{"CidrIp":"[IP]/32"}]}'
```

#### 3.6 Final Verification
```bash
aws ec2 describe-security-groups --group-ids sg-[ID]
```
**Status:** ✅ Both SSH and HTTP rules configured

### Screenshots
- `task3_create_security_group_output.png` - Group creation
- `task3_describe_sg_before_ingress.png` - Initial state
- `task3_codespace_public_ip.png` - Public IP retrieval
- `task3_authorize_ssh_and_describe.png` - SSH rule addition
- `task3_authorize_http_and_describe.png` - HTTP rule addition
- `task3_describe_sg_final.png` - Final configuration

---

## 💻 Task 4: EC2 Instance Management

### Objective
Create SSH key pair, launch EC2 instance, and establish SSH connection.

### Steps Completed

#### 4.1 Key Pair Creation
```bash
aws ec2 create-key-pair \
  --key-name MyED25519Key \
  --key-type ed25519 \
  --key-format pem \
  --query 'KeyMaterial' \
  --output text > MyED25519Key.pem
```

#### 4.2 Key Pair Verification
```bash
aws ec2 describe-key-pairs
```

#### 4.3 Subnet Discovery
```bash
aws ec2 describe-subnets --query "Subnets[*].[SubnetId,AvailabilityZone,VpcId]" --output table
```

#### 4.4 AMI Discovery
```bash
aws ec2 describe-images \
  --owners amazon \
  --filters "Name=name,Values=amzn2-ami-hvm-*-x86_64-gp2" \
  --query "Images | sort_by(@, &CreationDate) | [-1].[ImageId,Name]" \
  --output table
```

#### 4.5 Instance Launch
```bash
aws ec2 run-instances \
  --image-id ami-[ID] \
  --count 1 \
  --instance-type t3.micro \
  --key-name MyED25519Key \
  --security-group-ids sg-[ID] \
  --subnet-id subnet-[ID] \
  --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=MyServer}]"
```
**Instance ID:** i-[GENERATED_ID]

#### 4.6 Public IP Retrieval
```bash
aws ec2 describe-instances \
  --query "Reservations[*].Instances[*].[InstanceId,PublicIpAddress,State.Name]" \
  --output table
```

#### 4.7 SSH Connection
```bash
chmod 400 MyED25519Key.pem
ssh -i MyED25519Key.pem ec2-user@[PUBLIC_IP]
```
**Status:** ✅ Successfully connected to EC2 instance

#### 4.8 Instance State Management
```bash
# Stop instance
aws ec2 stop-instances --instance-ids i-[ID]

# Start instance
aws ec2 start-instances --instance-ids i-[ID]
```

### Screenshots
- `task4_create_keypair_output.png` - Key pair creation
- `task4_describe_keypairs.png` - Key pair listing
- `task4_run_instances_output.png` - Instance launch
- `task4_describe_instances_public_ip.png` - Public IP retrieval
- `task4_ssh_permission_error_and_fix.png` - SSH permission fix
- `task4_stop_start_terminate_commands.png` - State management

---

## 🔍 Task 5: AWS Resource Inspection

### Objective
Use AWS describe commands to inspect various resources.

### Commands Executed

#### 5.1 Security Groups
```bash
aws ec2 describe-security-groups
```

#### 5.2 VPCs
```bash
aws ec2 describe-vpcs
```

#### 5.3 Subnets
```bash
aws ec2 describe-subnets
```

#### 5.4 Instances
```bash
aws ec2 describe-instances
```

#### 5.5 Regions
```bash
aws ec2 describe-regions
```

#### 5.6 Availability Zones
```bash
aws ec2 describe-availability-zones
```

**Status:** ✅ All resources successfully inspected

### Screenshots
- `task5_describe_security_groups.png`
- `task5_describe_vpcs.png`
- `task5_describe_subnets.png`
- `task5_describe_instances.png`
- `task5_describe_regions.png`
- `task5_describe_availability_zones.png`

---

## 👥 Task 6: IAM Management

### Objective
Create IAM groups, users, attach policies, and manage access credentials.

### Steps Completed

#### 6.1 Group Creation
```bash
aws iam create-group --group-name MyGroupCli
aws iam get-group --group-name MyGroupCli
```

#### 6.2 User Creation
```bash
aws iam create-user --user-name MyUserCli
aws iam get-user --user-name MyUserCli
```

#### 6.3 User-Group Association
```bash
aws iam add-user-to-group --user-name MyUserCli --group-name MyGroupCli
```

#### 6.4 Policy Discovery & Attachment
```bash
aws iam list-policies --query "Policies[?contains(PolicyName, 'EC2')].{Name:PolicyName}" --output text
aws iam attach-group-policy \
  --group-name MyGroupCli \
  --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess
```

#### 6.5 Console Login Profile
```bash
aws iam create-login-profile \
  --user-name MyUserCli \
  --password [SECURE_PASSWORD] \
  --password-reset-required
```

#### 6.6 Password Change Policy
```bash
aws iam attach-group-policy \
  --group-name MyGroupCli \
  --policy-arn arn:aws:iam::aws:policy/IAMUserChangePassword
```

#### 6.7 Access Key Creation
```bash
aws iam create-access-key --user-name MyUserCli
aws iam list-access-keys --user-name MyUserCli
```

#### 6.8 Environment Variable Authentication
```bash
export AWS_ACCESS_KEY_ID=[KEY_ID]
export AWS_SECRET_ACCESS_KEY=[SECRET_KEY]
aws iam get-user --user-name MyUserCli
```

**Status:** ✅ IAM user fully configured with programmatic and console access

### Screenshots
- `task6_create_group_and_user.png`
- `task6_add_user_to_group_and_verify.png`
- `task6_policy_list_and_attach.png`
- `task6_create_login_profile_and_signin.png`
- `task6_create_access_key_output.png`
- `task6_env_exports_and_get_user_error.png`
- `task6_after_logout_and_get_user_success.png`

---

## 🔎 Task 7: Instance Filtering

### Objective
Use AWS CLI filters to query specific instances.

### Filters Applied

#### 7.1 Filter by Tag Name
```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=MyServer" \
  --query "Reservations[*].Instances[*].PublicIpAddress" \
  --output text
```

#### 7.2 Filter by Instance Type
```bash
aws ec2 describe-instances \
  --filters "Name=instance-type,Values=t3.micro" \
  --query "Reservations[].Instances[].InstanceId" \
  --output table
```

#### 7.3 Filter by Subnet
```bash
aws ec2 describe-instances \
  --filters "Name=subnet-id,Values=subnet-[ID]" \
  --query "Reservations[*].Instances[*].InstanceId" \
  --output table
```

#### 7.4 Filter by VPC
```bash
aws ec2 describe-instances \
  --filters "Name=vpc-id,Values=vpc-[ID]" \
  --query "Reservations[*].Instances[*].InstanceId" \
  --output table
```

**Status:** ✅ All filters successfully applied

### Screenshots
- `task7_filter_by_tag_public_ip.png`
- `task7_filter_by_instance_type.png`
- `task7_filter_by_subnet.png`
- `task7_filter_by_vpc.png`

---

## 📊 Task 8: Query Formatting

### Objective
Format AWS CLI outputs for professional reporting.

### Queries Executed

#### 8.1 Instance Details with Name
```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=MyServer" \
  --query "Reservations[*].Instances[*].[InstanceId,PublicIpAddress,Tags[?Key=='Name'].Value|[0]]" \
  --output table
```

#### 8.2 Instance State
```bash
aws ec2 describe-instances \
  --query "Reservations[*].Instances[*].[InstanceId,State.Name]" \
  --output table
```

#### 8.3 Instance Type and Availability Zone
```bash
aws ec2 describe-instances \
  --query "Reservations[*].Instances[*].[InstanceId,InstanceType,Placement.AvailabilityZone]" \
  --output table
```

**Status:** ✅ Professional table outputs generated

### Screenshots
- `task8_query_table_instances_name_ip.png`
- `task8_query_table_instance_state.png`
- `task8_query_table_instance_type_az.png`

---

## 🧹 Cleanup

### Objective
Remove all AWS resources to avoid charges.

### Resources Removed

#### Step 1: Terminate EC2 Instance
```bash
aws ec2 terminate-instances --instance-ids i-[ID]
```

#### Step 2: Delete EBS Volumes
```bash
aws ec2 describe-volumes --query "Volumes[*].[VolumeId,State]" --output table
aws ec2 delete-volume --volume-id vol-[ID]
```

#### Step 3: Delete Security Group & Key Pair
```bash
aws ec2 delete-security-group --group-id sg-[ID]
aws ec2 delete-key-pair --key-name MyED25519Key
rm MyED25519Key.pem
```

#### Step 4: Remove IAM Resources
```bash
aws iam delete-access-key --user-name MyUserCli --access-key-id [KEY_ID]
aws iam delete-login-profile --user-name MyUserCli
aws iam remove-user-from-group --user-name MyUserCli --group-name MyGroupCli
aws iam delete-user --user-name MyUserCli
aws iam detach-group-policy --group-name MyGroupCli --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess
aws iam delete-group --group-name MyGroupCli
```

#### Step 5: Final Verification
```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId,State.Name]" --output table
```

**Status:** ✅ All resources successfully removed

### Screenshots
- `cleanup_terminate_instance.png`
- `cleanup_delete_volumes_snapshots.png`
- `cleanup_delete_security_group_and_keypair.png`
- `cleanup_iam_users_deleted.png`
- `cleanup_summary.png`

---

## 💡 Key Learnings

### Technical Skills Acquired

1. **GitHub Codespaces**
   - Remote development environment setup
   - SSH connection management
   - CLI authentication and token management

2. **AWS CLI Proficiency**
   - Installation and configuration in Linux
   - Credential management
   - Resource creation and management

3. **EC2 Management**
   - Security group configuration
   - Ingress rule management
   - Key pair creation and usage
   - Instance lifecycle management (launch, stop, start, terminate)
   - SSH connectivity troubleshooting

4. **IAM Best Practices**
   - User and group management
   - Policy attachment
   - Access key generation
   - Environment variable authentication
   - Principle of least privilege

5. **AWS CLI Advanced Features**
   - Filtering resources by various attributes
   - JMESPath query syntax
   - Output formatting (table, JSON, text)
   - Resource inspection commands

### Best Practices Learned

- ✅ Never commit credentials to version control
- ✅ Use proper file permissions (chmod 400) for SSH keys
- ✅ Always clean up resources to avoid unexpected charges
- ✅ Use tags for resource organization
- ✅ Implement security groups with minimal required access
- ✅ Document all commands and configurations

---

## 🔧 Troubleshooting

### Common Issues Encountered and Solutions

#### Issue 1: SSH Permission Error
**Error:** `Permissions 0644 for 'MyED25519Key.pem' are too open`  
**Solution:**
```bash
chmod 400 MyED25519Key.pem
```

#### Issue 2: Security Group Deletion Failed
**Error:** Resource in use by instance  
**Solution:** Terminate instance first, then delete security group

#### Issue 3: IAM User Access Denied
**Error:** User lacks permissions for IAM operations  
**Solution:** Ensure appropriate policies are attached to user/group

#### Issue 4: Codespace Branch Not Found
**Error:** Invalid ref provided  
**Solution:** Initialize repository with README.md and push to main branch

---

## 📁 Repository Structure

```
LAB_9/
├── README.md                          # This file
├── Lab9.md                            # Lab manual
├── Lab9_solution.docx                 # Complete solution document
├── Lab9_solution.pdf                  # PDF version
└── screenshots/                       # All lab screenshots
    ├── task1_*.png
    ├── task2_*.png
    ├── task3_*.png
    ├── task4_*.png
    ├── task5_*.png
    ├── task6_*.png
    ├── task7_*.png
    ├── task8_*.png
    └── cleanup_*.png
```

---

## 📊 Lab Statistics

- **Total Tasks Completed:** 8 + Cleanup
- **Total Commands Executed:** 75+
- **Total Screenshots:** 45
- **Services Used:** EC2, IAM, VPC, STS
- **Resources Created:** 6 (Instance, Security Group, Key Pair, IAM Group, IAM User, Access Keys)
- **Resources Cleaned:** 6
- **Time Spent:** ~3 hours

---

## 🎓 Conclusion

This lab provided comprehensive hands-on experience with cloud infrastructure management using industry-standard tools. The combination of GitHub Codespaces and AWS CLI demonstrates modern DevOps practices and remote infrastructure management capabilities.

Key achievements:
- ✅ Successfully configured remote development environment
- ✅ Deployed and managed AWS EC2 infrastructure
- ✅ Implemented security best practices
- ✅ Gained proficiency in AWS CLI operations
- ✅ Completed full resource lifecycle management

---

## 📚 References

- [AWS CLI Documentation](https://docs.aws.amazon.com/cli/)
- [GitHub Codespaces Documentation](https://docs.github.com/en/codespaces)
- [GitHub CLI Documentation](https://cli.github.com/)
- [EC2 User Guide](https://docs.aws.amazon.com/ec2/)
- [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

---

## 👤 Contact Information

**Student:** Arshia Jadoon  
**Roll Number:** BSE-2023-014  
**Course:** Cloud Computing  
**Lab Number:** 09  
**Date Completed:** December 2024


*End of Documentation*
