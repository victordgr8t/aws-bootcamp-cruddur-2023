# Week 4 — Postgres and RDS

In week 4 of the AWS Cloud Project Bootcamp, I learnt how to provision an RDS instance and temporarily stop it, as well as remotely connect to the RDS instance. I also learnt how to programmatically update a security group rule and wrote several bash scripts for database operations.

I also learnt how to operate common SQL commands, create a schema SQL file by hand, work with UUIDs and PSQL extensions, and implement a Postgres client for Python using a connection pool. I also learnt how to troubleshoot common SQL errors and implement a Lambda function that runs in a VPC and commits code to RDS.

Furthermore, I worked with PSQL JSON functions to directly return JSON from the database and correctly sanitize parameters passed to SQL to execute. Overall, these skills are important for managing databases in AWS and ensuring their security and performance, as well as for developing and deploying applications that use those databases.


# TECHNICAL TASK 

- Launch Postgres locally via a container
- Seed our Postgres Database table with data
- Write a Postgres adapter
- Write a DDL (for creating schema)
- Write an SQL read query
- Write an SQL write query
- Provision an RDS Postgres instance
- Configure VPC Security Groups
- Configure local backend application to use production connection URL
- Add a caching layer using Momento Serverless Cache
- Propagate metrics from DDB to an RDS metrics table 


# TASK AHEAD

First to start the technical task we need to provision RDS using the command line in gitpod with the code below

```
    aws rds create-db-instance \
      --db-instance-identifier cruddur-db-instance \
      --db-instance-class db.t3.micro \
      --engine postgres \
      --engine-version  14.6 \
      --master-username cruddurroot \
      --master-user-password <yourpassword> \
      --allocated-storage 20 \
      --availability-zone us-east-1a \
      --backup-retention-period 0 \
      --port 5432 \
      --no-multi-az \
      --db-name cruddur \
      --storage-type gp2 \
      --publicly-accessible \
      --storage-encrypted \
      --enable-performance-insights \
      --performance-insights-retention-period 7 \
      --no-deletion-protection

```

<img width="1411" alt="Screen Shot 2023-03-23 at 4 58 59 PM" src="https://user-images.githubusercontent.com/63635704/227474609-8d1243f2-bc31-4f31-9c5d-0e980a9c814c.png">

I launched the RDS database from the command line with the codes above and decided to temporarily stop the database.


<img width="1049" alt="Screen Shot 2023-03-24 at 11 29 54 AM" src="https://user-images.githubusercontent.com/63635704/227481015-efd9eb69-2ffa-4171-811b-46010822b6ef.png">


Next step I launched postrges locally via a docker container to connect to psql via the psql client CLI tool 

```
  psql -Upostgres --host localhost
  
```

After I interactively logged into postgres I created a database called cruddur and viewed database created the  with ``\l``

```
  CREATE database cruddur;
```  

<img width="1084" alt="Screen Shot 2023-03-24 at 11 48 21 AM" src="https://user-images.githubusercontent.com/63635704/227486603-7351b817-7c8b-4cf7-897e-9d2bdd8fe4f0.png">

### Common PSQL

```
    \x on -- expanded display when looking at data
    \q -- Quit PSQL
    \l -- List all databases
    \c database_name -- Connect to a specific database
    \dt -- List all tables in the current database
    \d table_name -- Describe a specific table
    \du -- List all users and their roles
    \dn -- List all schemas in the current database
    CREATE DATABASE database_name; -- Create a new database
    DROP DATABASE database_name; -- Delete a database
    CREATE TABLE table_name (column1 datatype1, column2 datatype2, ...); -- Create a new table
    DROP TABLE table_name; -- Delete a table
    SELECT column1, column2, ... FROM table_name WHERE condition; -- Select data from a table
    INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...); -- Insert data into a table
    UPDATE table_name SET column1 = value1, column2 = value2, ... WHERE condition; -- Update data in a table
    DELETE FROM table_name WHERE condition; -- Delete data from a table

```










