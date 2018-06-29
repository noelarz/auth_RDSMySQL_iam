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