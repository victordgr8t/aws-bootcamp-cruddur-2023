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

```sql
  psql -Upostgres --host localhost
```

After I interactively logged into postgres I created a database called cruddur and viewed database created the  with ``\l``

```sql
  CREATE database cruddur;
```  

<img width="1084" alt="Screen Shot 2023-03-24 at 11 48 21 AM" src="https://user-images.githubusercontent.com/63635704/227486603-7351b817-7c8b-4cf7-897e-9d2bdd8fe4f0.png">

### Common PSQL

```sql
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

We can to login in cruddur database we just created with the commands:

```sql
    psql postgresql://postgres:<yourpassword>@localhost:5432/cruddur
```

we can also set environment variables to be able to login into the cruddur database

```sh 
   export CONNECTION_URL="postgresql://postgres:<yourpassword>@localhost:5432/cruddur"
   gp env CONNECTION_URL="postgresql://postgres:<yourpassword>@localhost:5432/cruddur"
   
   # command to login into to cruddur data
   psql $CONNECTION_URL

```

<img width="851" alt="Screen Shot 2023-03-24 at 5 22 27 PM" src="https://user-images.githubusercontent.com/63635704/227568244-eb497119-f990-4bcc-af53-fb81011bf871.png">


we will set environment variables for the connection to RDS we created earlier

```sh
    export PROD_CONNECTION_URL="postgresql://cruddurroot:<yourpassword>@cruddur-db-instance.cgnmcktcnzzo.us-east-1.rds.amazonaws.com:5432/cruddur
    gp env PROD_CONNECTION_URL="postgresql://cruddurroot:<yourpassword>@cruddur-db-instance.cgnmcktcnzzo.us-east-1.rds.amazonaws.com:5432/cruddur
```


# Import Script

We'll create a new SQL file called schema.sql and we'll place it in backend-flask/db

# Add UUID Extension
We are going to have Postgres generate out UUIDs. We'll need to use an extension called:

```sql
    CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```

# Create our tables

https://www.postgresql.org/docs/current/sql-createtable.html

we create a folder db and a fileinside called schema.sql

At the beginig we add the drop tables if existx:

```sql 
    # avoid duplicating the table 
   DROP TABLE IF EXISTS public.users;
   DROP TABLE IF EXISTS public.activities;
   
  CREATE TABLE public.users (
      uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
      display_name text NOT NULL,
      handle text NOT NULL,
      email text NOT NULL,
      cognito_user_id text NOT NULL,
      created_at TIMESTAMP default current_timestamp NOT NULL
   );

  CREATE TABLE public.activities (
      uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
      user_uuid UUID NOT NULL,
      message text NOT NULL,
      replies_count integer DEFAULT 0,
      reposts_count integer DEFAULT 0,
      likes_count integer DEFAULT 0,
      reply_to_activity_uuid integer,
      expires_at TIMESTAMP,
      created_at TIMESTAMP default current_timestamp NOT NULL
   );
   
```

Still in the backend directory we excute our schema:

```sql
    psql -Upostgres cruddur < db/schema.sql -h localhost
```

# Shell Script to Connect to DB

For things we commonly need to do we can create a new directory called bin

We'll create an new folder called bin to hold all our bash scripts.

```sh
    mkdir /workspace/aws-bootcamp-cruddur-2023/backend-flask/bin
```

We'll create a new bash script ```bin/db-connect```

```sql
    #! /usr/bin/bash

    psql $CONNECTION_URL
```

We'll make it executable:

```sh
    #change user permissions to executable
    chmod 744 bin/db-connect
    
    #excute script
    ./bin/db-connect
    
```

# Make prints nicer

We we can make prints for our shell scripts coloured so we can see what we're doing:
 
 ```sh
    CYAN='\033[1;36m'
    NO_COLOR='\033[0m'
    LABEL="db-schema-load"
    printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

```



# Shell script to drop the database

We'll create a new bash script ```bin/db-drop```

```sh
    #! /usr/bin/bash

    CYAN='\033[1;36m'
    NO_COLOR='\033[0m'
    LABEL="db-drop"
    printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

    NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
    psql $NO_DB_CONNECTION_URL -c "drop database cruddur;"
    
```

## See what connections we are using


We'll create a new bash script `bin/db-sessions`

```sh
#! /usr/bin/bash
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-sessions"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

NO_DB_URL=$(sed 's/\/cruddur//g' <<<"$URL")
psql $NO_DB_URL -c "select pid as process_id, \
       usename as user,  \
       datname as db, \
       client_addr, \
       application_name as app,\
       state \
from pg_stat_activity;"
```

We can see the sessions opened in the database:

<img width="1141" alt="Screen Shot 2023-03-24 at 8 50 38 PM" src="https://user-images.githubusercontent.com/63635704/227705214-c0ddd992-27db-4826-b1bb-783f25a6a1bf.png">



# Shell script to create the database

We'll create a new bash script ```bin/db-create```

```sh
   #! /usr/bin/bash

    CYAN='\033[1;36m'
    NO_COLOR='\033[0m'
    LABEL="db-create"
    printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

    NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
    psql $NO_DB_CONNECTION_URL -c "create database cruddur;"
    
```


# Shell script to load the schema
We'll create a new bash script ```bin/db-schema-load```

```sh
    #! /usr/bin/bash

    CYAN='\033[1;36m'
    NO_COLOR='\033[0m'
    LABEL="db-schema-load"
    printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

    schema_path="$(realpath .)/db/schema.sql"
    echo $schema_path

    if [ "$1" = "prod" ]; then
      echo "Running in production mode"
      URL=$PROD_CONNECTION_URL
    else
      URL=$CONNECTION_URL
    fi

    psql $URL cruddur < $schema_path

```


# Shell script to load the seed data

Create the script backend-flask/bin/db-seed

```sh
    #! /usr/bin/bash

    CYAN='\033[1;36m'
    NO_COLOR='\033[0m'
    LABEL="db-seed"
    printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

    seed_path="$(realpath .)/db/seed.sql"
    echo $seed_path

    if [ "$1" = "prod" ]; then
      echo "Running in production mode"
      URL=$PROD_CONNECTION_URL
    else
      URL=$CONNECTION_URL
    fi

    psql $URL cruddur < $seed_path
    
```

Create the file for the seed in backend-flask/db/seed.sql

```sql
   -- this file was manually created
INSERT INTO public.users (display_name, handle, email, cognito_user_id)
VALUES
  ('Victor Modebe', 'victormodebe' , 'jxxx@gmail.com', 'MOCK'),
  ('Andrew Bayko2', 'bayko2' , 'qxxxx@gmail.com', 'MOCK');

INSERT INTO public.activities (user_uuid, message, expires_at)
VALUES
  (
    (SELECT uuid from public.users WHERE users.handle = 'victormodebe' LIMIT 1),
    'This was imported as seed data!',
    current_timestamp + interval '10 day'
  )
      
```

Next we execute backend-flask/bin/db-seed as shown below

<img width="819" alt="Screen Shot 2023-03-24 at 6 05 40 PM" src="https://user-images.githubusercontent.com/63635704/227580135-0f59410f-da69-4fbc-92e2-5992593d4495.png">

## Easily setup (reset) everything for our database

to recreate,load the cruddur database will create the script backend-flask/bin/db-setup to do all this:

```sh
#! /usr/bin/bash
-e # stop if it fails at any point

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-setup"
printf "${CYAN}==== ${LABEL}${NO_COLOR}\n"

bin_path="$(realpath .)/bin"

source "$bin_path/db-drop"
source "$bin_path/db-create"
source "$bin_path/db-schema-load"
source "$bin_path/db-seed"
```
<img width="1162" alt="Screen Shot 2023-03-24 at 9 12 32 PM" src="https://user-images.githubusercontent.com/63635704/227705058-c7bc966e-b035-43e6-804f-ebf94afe7b88.png">





## Install Postgres Client

We need to set the env var for our backend-flask application:

```yml
  backend-flask:
    environment:
      CONNECTION_URL: "${CONNECTION_URL}"
```

https://www.psycopg.org/psycopg3/

We'll add the following to our `requirments.txt`

```
psycopg[binary]
psycopg[pool]
```

```
pip install -r requirements.txt
```

## DB Object and Connection Pool

Create a file lib/db.py and inut the following code:

```py

    from psycopg_pool import ConnectionPool
    import os

    def query_wrap_object(template):
      sql = f"""
      (SELECT COALESCE(row_to_json(object_row),'{{}}'::json) FROM (
      {template}
      ) object_row);
      """
      return sql

    def query_wrap_array(template):
      sql = f"""
      (SELECT COALESCE(array_to_json(array_agg(row_to_json(array_row))),'[]'::json) FROM (
      {template}
      ) array_row);
      """
      return sql

    connection_url = os.getenv("CONNECTION_URL")
    pool = ConnectionPool(connection_url)
    
```


In our home activities we'll replace our mock endpoint with real api call:


```py
        from lib.db import pool, query_wrap_object, query_wrap_array

      sql = query_wrap_array("""
      SELECT
        activities.uuid,
        users.display_name,
        users.handle,
        activities.message,
        activities.replies_count,
        activities.reposts_count,
        activities.likes_count,
        activities.reply_to_activity_uuid,
        activities.expires_at,
        activities.created_at
      FROM public.activities
      LEFT JOIN public.users ON users.uuid = activities.user_uuid
      ORDER BY activities.created_at DESC
      """)
      print(sql)
      with pool.connection() as conn:
        with conn.cursor() as cur:
          cur.execute(sql)
          # this will return a tuple
          # the first field being the data
          json = cur.fetchone()
      return json[0]
```

Open the frontend link to test the web-app


<img width="1265" alt="Screen Shot 2023-03-24 at 10 33 40 PM" src="https://user-images.githubusercontent.com/63635704/227704423-37db42e7-cda3-45a7-9026-3f08a4431f2b.png">

## Connect to RDS via Gitpod

In order to connect to the RDS instance we need to provide our Gitpod IP and whitelist for inbound traffic on port 5432.

```sh
GITPOD_IP=$(curl ifconfig.me)
```

We'll create an inbound rule for Postgres (5432) and provide the GITPOD ID.

We'll get the security group rule id so we can easily modify it in the future from the terminal here in Gitpod.

```sh
export DB_SG_ID="sg-8a2c86ba"
gp env DB_SG_ID="sg-8a2c86ba"
export DB_SG_RULE_ID="sgr-067133a5a49"
gp env DB_SG_RULE_ID="sgr-067133a5a49d"
```

Whenever we need to update our security groups we can do this for access.
```sh    
aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={Description=Gitpod,IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"

```

https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-security-group-rules.html#examples


## Test remote access

We'll update your URL for production use case

```sh
export PROD_CONNECTION_URL="postgresql://cruddurroot:******@cruddur-db-instance.czgmtaw1bmat.us-east-1.rds.amazonaws.com:5432/cruddur"
gp env PROD_CONNECTION_URL="postgresql://cruddurroot:******@cruddur-db-instance.czgmtaw1bmat.us-east-1.rds.amazonaws.com:5432/cruddur"
```

We'll test that it works in Gitpod:

``` sh
psql $PROD_CONNECTION_URL
```
<img width="1144" alt="Screen Shot 2023-03-24 at 11 53 57 PM" src="https://user-images.githubusercontent.com/63635704/227704652-0afb08a0-0d29-4a60-b3da-90a509f78403.png">


# Update Bash scripts for production

```sh
if [ "$1" = "prod" ]; then
  echo "Running in production mode"
else
  echo "Running in development mode"
fi
```

We'll update:
- db-connect
- db-schema-load

## Update Gitpod IP on new env var

We'll add a command step for postgres in gitpod.yml:

```sh
   command: |
      export GITPOD_IP=$(curl ifconfig.me)
      source "$THEIA_WORKSPACE_ROOT/backend-flask/bin/rds-update-sg-rule"
```

And test the connection to aws postgres with the script in backend directory:

``` sh
./bin/db-connect prod
```
<img width="1154" alt="Screen Shot 2023-03-25 at 12 27 13 AM" src="https://user-images.githubusercontent.com/63635704/227704822-8648604f-007b-47df-9966-4ce7375d961a.png">

## Load schema in prod

In backend directory , we execute:

``` sh
./bin/db-schema-load prod
```













