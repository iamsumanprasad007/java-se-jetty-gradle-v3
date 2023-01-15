This pipeline will be used for building & deploying java application.

1) Variable initialization

BuildName="version-$BUILD_NUMBER"
BucketName="testbucket707"
BucketKey="S3-builds-Storage"
ApplicationName="testApp"
EnvironmentName="Testapp-env"

2) First of all, this pipeline pulls source code from github repository. This repository contains a sample java application code https://github.com/farahgul13/testapp...

3) Then pipeline make package in .zip format for this testappsample source code.
cd java-se-jetty-gradle-v3
zip -r $BuildName.zip *

4) Then pipeline upload this package .zip package at S3 storage.
aws s3 cp $BuildName.zip s3://$BucketName --region ap-south-1

5) Then remove this package from your local machine i.e. EC2 ubuntu instance 
rm /var/lib/jenkins/workspace/Build_Deploy/java-se-jetty-gradle-v3/$BuildName.zip

6) Then pipeline create an application version for latest build.
aws elasticbeanstalk create-application-version --application-name "$ApplicationName" --version-label "$BuildName" --description "Build created from JENKINS. Job:$JOB_NAME, BuildId:$BUILD_DISPLAY_NAME, GitCommit:$GIT_COMMIT, GitBranch:$GIT_BRANCH" --source-bundle S3Bucket=$BucketName,S3Key=$BuildName.zip --region ap-south-1

7) Deploy this latest application version at elastic beanstalk
aws elasticbeanstalk update-environment --environment-name "$EnvironmentName" --version-label "$BuildName" --region ap-south-1

Pre-requisite:

Before starting pipeline creation, I would like to discuss about pre-requisite for creating this pipeline. 

1) How to create AWS account? 
https://www.linkedin.com/pulse/how-cr...
You should have AWS account, if you don’t have AWS account, create it by using this link. 

2) AWS IAM - How to create Group, Role and User? 
https://www.linkedin.com/pulse/aws-ia...
Then you should have user, group & role.

3) How to create AWS EC2 ubuntu instance?
https://www.linkedin.com/pulse/how-cr...
Then you should have EC2 instance. I install ubuntu on this instance.

4) Install JDK and Jenkins at EC2 ubuntu instance
Install JDK
Ubuntu 18.04 LTS use Java 9 as default java
Jenkins 2.107.2 still use Java 8
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt install oracle-java8-installer
$ sudo update-alternatives --config java
https://stackoverflow.com/questions/4...

Install Jenkins
https://www.digitalocean.com/communit...

5) Create a bucket at AWS S3 storage .You should have at least one bucket at S3 storage.

6) Create new application at AWS elastic beanstalk.You should have at least one test environment at elastic bean for deploying application.
