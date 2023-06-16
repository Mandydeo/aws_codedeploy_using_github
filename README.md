# aws_codedeploy_using_github
Step 1: 
Create repository on Github.

Project must contain appspec.yml file in the root directory, which is used by the CodeDeploy service to manage deployment otherwise, deployments fail.

The following is the correct format for AppSpec file spacing. The numbers in square brackets indicate the number of spaces that must occur between items. For example, [4] means to insert four spaces between the items. CodeDeploy raises an error that might be difficult to debug if the locations and number of spaces in an AppSpec file are not correct.

version:[1]version-number
os:[1]operating-system-name
files:
[2]-[1]source:[1]source-files-location
[4]destination:[1]destination-files-location
permissions:
[2]-[1]object:[1]object-specification
[4]pattern:[1]pattern-specification
[4]except:[1]exception-specification
[4]owner:[1]owner-account-name
[4]group:[1]group-name
[4]mode:[1]mode-specification
[4]acls: 
[6]-[1]acls-specification 
[4]context:
[6]user:[1]user-specification
[6]type:[1]type-specification
[6]range:[1]range-specification
[4]type:
[6]-[1]object-type
hooks:
[2]deployment-lifecycle-event-name:
[4]-[1]location:[1]script-location
[6]timeout:[1]timeout-in-seconds
[6]runas:[1]user-name
Here is an example of a correctly spaced AppSpec file:


version: 0.0os: linuxfiles:
  - source: /
    destination: /var/www/html/WordPresshooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: root
  AfterInstall:
    - location: scripts/change_permissions.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start_server.sh
    - location: scripts/create_test_db.sh
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server.sh
      timeout: 300
      runas: root
The SOURCE file is index.html so ensure every other file within repository is linked to this.


Step 2:
Create IAM roles for EC2 and CodeDeploy  

Step 3:
Launch an EC2 Instance

Create name, use Amazon Linux 2023 AMI, instance type t2.micro, key pair launch without key, IAM instance profile or role, select role created, customize security group, enable Metada v1 and v2 and under user data, input the following:

#!/bin/bash
sudo yum -y update
sudo yum -y install ruby
sudo yum -y install wget
cd /home/ec2-user
wget https://aws-codedeploy-us-east-2.s3.us-east-2.amazonaws.com/latest/install
sudo chmod +x ./install
sudo ./install auto
sudo yum install -y python-pip
sudo pip install awscli


Step 4:
Create deployment Application on CodeDeploy

Also create a Deployment Group without load balancer.

Also create pipeline; skip build stage
