## Authenticate to Your AWS MySQL RDS Instance via IAM

## Prerequesites
- Create RDS MySQL Database
- Create IAM Policy that allows a user to connect via a MySQL User
- Create IAM User and associate IAM Policy
- Configure the new user credentials in the awscli credential provider
- Bash script to generate the auth token and authenticate to RDS via Token instead of password
 
## Create the RDS Database:
aws rds create-db-instance \
    --db-instance-identifier dash \
    --db-instance-class db.t2.micro \
    --engine MySQL \
    --allocated-storage 20 \
    --master-username dbadmin \
    --master-user-password dbmaspass \
    --region us-esst-1 \
    --enable-iam-database-authentication

## Create the Database Account:
$ mysql -u dbadmin -h dash.abcdefgh.eu-west-1.rds.amazonaws.com -p

$ mysql> CREATE USER mydbaccount IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS';
$ mysql> FLUSH PRIVILEGES;

## Creating the Databases and Granting Permissions
$ mysql> create database db100;

$ mysql> create database db200;

$ mysql> use db100;

$ mysql> create table foo (name VARCHAR(20), age INT);

$ mysql> insert into foo values('jim', 31);

$ mysql> insert into foo values('anna', 32);

$ mysql> use db200;

$ mysql> create table foo (location VARCHAR(255));

$ mysql> insert into foo values('mexico');

$ mysql> insert into foo values('canada');

$ mysql> insert into foo values('bahams');

$ mysql> grant select on db100.* to 'mydbuser';

$ mysql> grant select on db200.* to 'mydbuser';

$ mysql> create database db300;

$ mysql> use db300;

$ mysql> create table foo (passwords VARCHAR(255));

$ mysql> insert into foo values('yourdadpass');

$ mysql> insert into foo values('yoursistepass');

$ mysql> flush privileges;

### IAM Permissions to allow our user to authenticate to our RDS.
$ aws configure --profile dbuser

AWS Access Key ID [None]: xxxxxxxxxxxxx

AWS Secret Access Key [None]: xxxxxxxxxxxxxxxxxx

Default region name [None]: us-east-1

Default output format [None]: json

## To get the DB ResourceId:
$ aws rds describe-db-instances --db-instance-identifier dash | jq -r ".DBInstances[].DbiResourceId
db-123456789ABCDEFGH


### IAM Policy and associate it with the new user account:
{
   "Version": "2012-10-17",
   "Statement": [
      {
           "Sid": "RDSIAMAUTH",
         "Effect": "Allow",
         "Action": [
             "rds-db:connect"
         ],
         "Resource": [
             "arn:aws:rds-db:us-eust-1:123456789012:dbuser:db-123456789ABCDEFGH/mydbaccount"
         ]
      }
   ]
}


### token_gen_iam
Bash script that will generate the authentication token to be used as the password (expires every 15 minutes)

### mysql_iam_wrapper
Bash script to connect to my DB

$ mysql-iam prod dash.us-east-1.amazonaws.com mydbaccount
