Below are my notes for this, There are certain parts that are done with the GUI.

0. Create and configure EC2 instance to deploy application to


aws ec2 describe-images --owners self amazon (worry about this later)

aws ec2 run-instances --image-id ami-0443305dabd4be2bc \
--count 1 --instance-type t2.micro \
--key-name basic-key \
--region us-east-2 \
--tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=008-cicd-web}]'


1. Configure EC2 instance with required packages and code deploy

sudo yum upgrade -y
sudo yum install ruby -y
sudo yum install wget -y
wget https://aws-codedeploy-eu-central-1.s3.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
sudo service codedeploy-agent status

2. Go into the GUI and make a service role for codedeploy, with whatever you need for it to work


3. create application in code-deploy

aws deploy create-application --application-name 008-web-project --region us-east-2


4. (if using s3 for revision) bundle deployment files and send them to s3

aws deploy push --application-name 007-udemy-project \
--s3-location s3://007-udemy-project/part1/dev/deploy/mywebapp.zip \
--source /home/ec2-user/jmanage/projects/007/dev/mywebapp/mywebapp/ \
--ignore-hiden-files

4. (if using github for revision)

dump all your files into the github repo make sure that there are no sub directories, code deploy has issues with those


5. create deployment group for application

aws deploy create-deployment-group \
--application-name 008-web-project \
--deployment-group-name 008-web-project-dg \
--deployment-config-name CodeDeployDefault.AllAtOnce \
--ec2-tag-filters Key=Name,Value=008-web-project,Type=KEY_AND_VALUE \
--service-role-arn arn:aws:iam::059767637825:role/codedeploy \
--region us-east-2


6. go into code deploy and authenticate your aws account with github

- this step is really strange, you need to create a project and select github 

- then authenticate

- and then cancel the project

=====================

eneter the below two fields for the git IDs

dota247/008-web-project

f8f4afa8d857831453b0c63417b16046c253126c



7. Deploy application with code-deploy

aws deploy create-deployment \
--application-name 008-web-project \
--github-location repository=dota247/008-web-project,commitId=12b837366ccf3cc27ebf7ecade0cbd2fba87e211 \
--deployment-group-name 008-web-project-dg \
--description "deployment for dev environment" \
--region us-east-2

-copy deployment id

- if it fails run the command again

8. commit a new web app and observer if the URL updates







