# <h1 id="topheading">AWS CLI Helpful Tips and Commands</h1>

## Objectives

AWS CLI Tips and Tricks

## IAM Commands:
List IAM Users:
aws iam list-users

Create User:
aws iam create-user --user-name static-blog

Check if root account have MFA enabled:
aws iam get-account-summary | grep "AccountMFAEnabled"

Switch to new user:
aws configure --profile <profile-name>

Create a group:
aws iam create-group --group-name FullAdmins

Delete a group
aws iam delete-group --group-name FullAdmins

List all policies:
aws iam list-policies

Get a specific policy
aws iam get-policy --policy-arn <value>

List all users, groups, and roles, for a given policy
aws iam list-entities-for-policy --policy-arn <value>

List policies for a group:
aws iam list-attached-group-policies --group-name FullAdmins

Add a user to a group:
aws iam add-user-to-group --group-name FullAdmins --user-name aws-admin2

List users, for a given group
aws iam get-group --group-name FullAdmins

List groups, for a given user
aws iam list-groups-for-user --user-name aws-admin2

Remove a user from a group
aws iam remove-user-from-group --group-name FullAdmins --user-name aws-admin2

Remove a policy from a group
aws iam detach-group-policy --group-name FullAdmins --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

Delete a group
aws iam delete-group --group-name FullAdmins

List all IAM users, groups, and roles that the policy is attached to:
aws iam list-entities-for-policy --policy-arn arn:aws:iam::aws:policy/aws-service-role/AWSSupportServiceRolePolicy

List IAM policies while filtering for AWSSUpportAccess:
aws iam list-policies --query "Policies[?PolicyName == 'AWSSupportAccess']"

List Server Certificates:
aws iam list-server-certificates

rmine is root MFA is enabled:
aws iam list-virtual-mfa-devices

Provide a credential report for determining the last time the root user was used:
aws iam generate-credential-report
aws iam get-credential-report --query 'Content' --output text | base64 -d | cut -d, -f1,5,11,16 | grep -B1 '<root_account>'

Get Password Policy Details:
aws iam get-account-password-policy

To set a minimum password length of 14 characters, do the following:
aws iam update-account-password-policy --minimum-password-length 14

Define password re-use policy:
aws iam update-account-password-policy --password-reuse-prevention 24

Delete user-id access key and username:
aws iam delete-access-key --access-key-id <access-key-id-listed> --user-name <users-name>

List details of a specific user:
aws iam list-access-keys --user-name <user-name>

Get list of policies:
aws iam list-policies --only-attached --output text


## IAM User Commands:

List all user's usernames
aws iam list-users --output text | cut -f 6

Get the user name for the current session you are in:
aws iam get-user --profile lab22

Armed with a username we can now see bob's policies:
aws iam list-user-policies --profile lab22 --user-name bob

Check to see if there are any policies for the user:
aws iam list-attached-user-policies --profile lab22 --user-name bob

List current user's access keys
aws iam list-access-keys

Create new user
aws iam create-user --user-name aws-admin2

Create multiple new users, from a file
allUsers=$(cat ./user-names.txt)
for userName in $allUsers; do aws iam create-user --user-name $userName; done

List all users
aws iam list-users --no-paginate

Get a specific user's info
aws iam get-user --user-name aws-admin2

Delete one user
aws iam delete-user --user-name aws-admin2

Delete all users
allUsers=$(aws iam list-users --output text | cut -f 6);
allUsers=$(cat ./user-names.txt)
for userName in $allUsers; do
aws iam delete-user \
--user-name $userName
done


## S3 Commands:

List all of the S3 Buckets
aws s3 ls

View contents of a bucket:
aws s3 ls s3://<name-of-bucket> --recursive

Create S3 bucket name:
aws s3 mb s3://<bucket-name>

Copy single file to S3 bucket:
aws s3 cp "file-to-copy" s3://remote-bucket-to-copy-to

Copy multiple files at once to S3 bucket:
aws s3 cp "Folder-location-to-copy-from/" s3://remote-bucket-to-copy-to --recursive

List contents in remote bucket:
aws s3 ls s3://mybucket

Deleting files in bucket:
aws s3 rm s3://mybucket --recursive

List Amazon S3 Buckets (from within an EC2 instance you can obtain this information, too):
aws s3api list-buckets

Export the bucket policy to a json file:
aws s3api get-bucket-policy --bucket <bucket_name> --query Policy --output text > policy.json

Find the public access setting on that bucket:
aws s3api get-public-access-block --bucket <name-of-the-bucket>

Set Public access to true on a bucket:
aws s3api put-public-access-block --bucket <name-of-bucket> --public-access- block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPu blicBuckets=true"

List S3 contents using a specific profile:
aws s3 ls --profile <profile-name>

Pushing Content:
aws s3 sync . s3://www.mybucket.com/ \
 --acl public-read \
 --delete \
 --profile staticblog


## Password Policies Commands:
List policy
aws iam get-account-password-policy

Set policy
aws iam update-account-password-policy \
    --minimum-password-length 12 \
    --require-symbols \
    --require-numbers \
    --require-uppercase-characters \
    --require-lowercase-characters \
    --allow-users-to-change-password

Delete policy
aws iam delete-account-password-policy


## EC2 Commands:

List all Instances:
aws ec2 describe-instances

List status of all instances
aws ec2 describe-instance-status

Start an Instance:
aws ec2 start-instances --instance-ids <id number of instance>

Delete an instance:
aws ec2 terminate-instances --instance-ids  <id number of instance>

Create a new instance
aws ec2 run-instances --image-id ami-f0e7d19a --instance-type t2.micro --security-group-ids sg-00000000 --dry-run

Stop an instance
aws ec2 terminate-instances --instance-ids <instance_id>

Look for Private IP Addresses:
aws ec2 describe-instances --profile lab22 | jq '.Reservations[].Instances[] | "\(.InstanceId) \(.PrivateIpAddress)"'


## Keypairs Commands:

List all keypairs:
aws ec2 describe-key-pairs

Create a Key Pair:
aws ec2 create-key-pair --key-name <value>

Import Key Pair:
aws ec2 import-key-pair --key-name keyname_test --public-key-material file:///home/apollo/id_rsa.pub

Delete Key Pair:
aws ec2 delete-key-pair --key-name keyname_test


## Access Keys Commands:

List all access keys
aws iam list-access-keys

List access keys of a specific user
aws iam list-access-keys --user-name aws-admin2

Create a new access key
aws iam create-access-key --user-name aws-admin2 --output text | tee aws-admin2.txt

List last access time of an access key
aws iam get-access-key-last-used --access-key-id AKIAINA6AJZY4EXAMPLE

Deactivate an acccss key
aws iam update-access-key --access-key-id AKIAI44QH8DHBEXAMPLE --status Inactive --user-name aws-admin2

Delete an access key
aws iam delete-access-key --access-key-id AKIAI44QH8DHBEXAMPLE --user-name aws-admin2


## Security Group Commands:
List Security Groups:
aws ec2 describe-security-groups --group-names

Security Group: Remove Rule:
aws ec2 revoke-security-group-ingress --group-name SEC545 --protocol icmp --port -1 --cidr 0.0.0.0/0

Search and list all AWS Security Group Names:
aws ec2 describe-security-groups |grep GroupName

Open Port 22 and 80:
aws ec2 authorize-security-group-ingress --group-id sg-0000000 --protocol tcp --port 22 --cidr 0.0.0.0/24
aws ec2 authorize-security-group-ingress --group-id sg-0000000 --protocol tcp --port 80 --cidr 0.0.0.0/24

Remove Port 22 and 80:
aws ec2 revoke-security-group-ingress --group-id sg-0000000 --protocol tcp --port 22 --cidr 0.0.0.0/24
aws ec2 revoke-security-group-ingress --group-id sg-0000000 --protocol tcp --port 80 --cidr 0.0.0.0/24

Delete a security group:
aws ec2 delete-security-group --group-id sg-0000000


## Tags:

List the tags of an instance:
aws ec2 describe-tags

Add a tag to an instance
aws ec2 create-tags --resources "ami-1a2b3c4d" --tags Key=name,Value=debian

Delete a tag on an instance
aws ec2 delete-tags --resources "ami-1a2b3c4d" --tags Key=Name,Value=


## Cloudtrail - Logging and Auditing Commands:

List all trails
aws cloudtrail describe-trails

List all S3 buckets
aws s3 ls

Create a new trail
aws cloudtrail create-subscription --name awslog --s3-new-bucket awslog2016

List the names of all trails
aws cloudtrail describe-trails --output text | cut -f 8

Get the status of a trail
aws cloudtrail get-trail-status --name awslog

Delete a trail
aws cloudtrail delete-trail --name awslog

Delete the S3 bucket of a trail
aws s3 rb s3://awslog2016 --force

Add tags to a trail, up to 10 tags
aws cloudtrail add-tags --resource-id awslog --tags-list "Key=log-type,Value=all"

List the tags of a trail
aws cloudtrail list-tags --resource-id-list

Remove a tag from a trail
aws cloudtrail remove-tags --resource-id awslog --tags-list "Key=log-type,Value=all"

## VPCs
List all non default VPC IDs:
aws ec2 describe-vpcs --filter "Name=isDefault,Values=false" --query Vpcs[].VpcId --output table

Create a new security group with the CLI (replace the VPC ID with your own).
aws ec2 create-security-group --group-name HTTP_SSH_Access --description " HTTP_SSH_Access" --vpc-id <your-VPC-ID>

Create rules to allow port 22 and 443:
aws ec2 authorize-security-group-ingress --group-id <Group ID #> --cidr 0.0.0.0/0 --protocol tcp --port 443
aws ec2 authorize-security-group-ingress --group-id <Group ID #> --cidr 0.0.0.0/0 --protocol tcp --port 22

View the current rules:
aws ec2 describe-security-groups --group-ids

## AWS AMI Creation

Create S3 folder with OVA, container.json, role-policy.json files

containers.json
role-policy.json

From AWS CLI:
aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document file://role-policy.json
aws ec2 import-image --platform linux --license-type BYOL --disk-containers "file://containers.json" --region us-east-1 --description


Check Status:
aws ec2 describe-import-image-tasks --import-task-ids import-ami-0ee050c9f021fb12b


Web Tips:
https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html
https://gitlab.com/sansappsec/aws_import

## Misc Commands:
Attempt to obtain keys to escalate privileges:
curl http://169.254.169.254/latest/meta-data/iam/security-credentials

Grab Key Name and add to new query:
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/ec2-kms-role-bright-shrew; printf "\n"

Grab Metadata from EC2 Instance:
curl http://169.254.169.254/latest/meta-data/  <--Include last forward slash

Grab ec2-metadata content with wget:
wget https://s3.amazonaws.com/ec2metadata/ec2-metadata
chmod u+x ec2-metadata
ec2-metadata --help

Public Hostname:
ec2-metadata -p

Availability Zone:
ec2-metadata -z

User Metadata:
curl http://169.254.169.254/latest/user-data/


## Other Links/Resources:
https://devhints.io/awscli
https://riptutorial.com/aws-cli/example/22749/aws-cli-cheat-sheet---list-of-all-cli-commands
