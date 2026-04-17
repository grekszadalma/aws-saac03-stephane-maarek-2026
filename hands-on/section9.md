Amazon RDS hands on

Search for Aurora and RDS -> Databases -> Create database. Full configuration and creation method Full configuration.

We can choose from multiple types of database instances but we choose MySQL. For the password, it is possible to use AWS Secrets Manager but it's not in the Free Tier.
So we choose Self managed. Keep login name admin and choose password.
Keep everything default except Access where we need to choose Public to be able to reach our DB outside of the VPC (it will have a public address).

Then create new VPC security group. Name it demo-rds. Database port can stay 3306 (MySQL port).

Wait until the DB is created. Then in DBeaver, connect to the DB like this: 
<img width="821" height="742" alt="image" src="https://github.com/user-attachments/assets/680ccf6c-3caf-4d8b-9baa-898dc50c2e82" />

The name of the DB is on the Connectivity and Security page of the DB.

