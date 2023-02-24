# Week 1 — App Containerization

In the second week of building the Cruddur micro-blogging app, we continued the process by configuring our backend with the installation of Flask and Python. This allowed us to start building out the server-side of our application, which will handle data storage, user authentication, and other critical functions.

We also started building out the frontend of the application using JavaScript and React, which required us to install npm to manage dependencies and build tools. This allowed us to start building out the user interface and user experience of the application.

To ensure that our application is scalable and easy to deploy, we decided to containerize it using Docker. We created a Docker Compose file with frontend, backend, Postgres, and DynamoDB commands, which allowed us to quickly spin up and test our application in a consistent and isolated environment.

Finally, we activated the notification settings on both the frontend and backend, which will allow our users to receive real-time updates and alerts about new posts and other activity on the platform.

Overall, we made significant progress in the second week of building the Cruddur micro-blogging app, and we are excited to continue developing and refining the application in the coming weeks.



# Tasks completed  in Week 1


### We have two options to perform the next task

- First we create a ' docker-compose.yml ' file then define all dependencies & commands for both frontend and backend then build and run 
- secondly we can create a dockerfile for each (frontend and backend) define dependencies then create a network. 
  - Next build the docker images then run the containers with the docker network and assign a port to the container to enable tcp connection or https access. 

```sh
docker run --name <container_name> --network <network_name> -d <container_name>
docker run --name <container_name> --network <network_name> -d -p <****:****> <container_name>
```


I will choose the first option which is very efficient and less coding 

- Now i will create a docker-compose.yml file in the root directory of the project with code shown below.

```sh
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

networks: 
  internal-network:
    driver: bridge
    name: cruddur
    
```


Before running the docker up command, first install the dependencies for the backend and frontend individually.

### Backend
```sh
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
pip3 install -r requirements.txt
python3 -m flask run --host=0.0.0.0 --port=4567

```

### Frontend
```sh
cd frontend-react-js
npm install

```


Next we run the docker compose command, make sure to return to the root folder of the project to avoid intalling in the wrong folder.

```sh
docker compose up 

```


- Make sure to unlock the port on the ports tab next to terminal in vscode
- Unlock the link for 4567 & 3000 to enable view in  browser
- append to the url to `/api/activities/home` for 4567 to view contents

### We should get the results below if the everything works as intended
<img width="994" alt="Screen Shot 2023-02-24 at 2 22 38 PM" src="https://user-images.githubusercontent.com/63635704/221178232-c74723f3-796d-4739-8577-470e948e8778.png">

<img width="1304" alt="Screen Shot 2023-02-24 at 3 14 26 PM" src="https://user-images.githubusercontent.com/63635704/221187961-38735ce5-f659-418d-8eb9-39c8c3e5d526.png">

<img width="761" alt="Screen Shot 2023-02-24 at 3 31 35 PM" src="https://user-images.githubusercontent.com/63635704/221191240-5c6fc900-b9dc-43df-90d4-5e106f00cf37.png">


### We should get this result if the backend and frontend are communicating successfully
<img width="1222" alt="Screen Shot 2023-02-24 at 3 12 56 PM" src="https://user-images.githubusercontent.com/63635704/221188018-da66f458-ce1a-4bd9-a6e6-04d9616499e1.png">

  - Next we will add some code  to the gitpod.yml to spin up installation of dependencies when opened 

```sh
- name: Initialize Frontend and Backend
    init: |
      cd /workspace/aws-bootcamp-cruddur-2023/backend-flask
      pip install --upgrade pip
      pip3 install -r requirements.txt
      cd /workspace/aws-bootcamp-cruddur-2023/frontend-react-js
      npm install  

```



## Next step we will add DynamoDB Local and Postgres in our containers 
Dynamodb and Postgres will be used for a later time but for this task we are required to add them to the containers.

- we will write codes for both dynamo and postrges and update it in our docker-compose.yml file.

```sh
services:
  db:
      image: postgres:13-alpine
      restart: always
      environment:
        - POSTGRES_USER=postgres
        - POSTGRES_PASSWORD=password
      ports:
        - '5432:5432'
      volumes: 
        - db:/var/lib/postgresql/data

  dynamodb-local:
      # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
      # We needed to add user:root to get this working.
      user: root
      command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
      image: "amazon/dynamodb-local:latest"
      container_name: dynamodb-local
      ports:
        - "8000:8000"
      volumes:
        - "./docker/dynamodb:/home/dynamodblocal/data"
      working_dir: /home/dynamodblocal

```



- Then we will input command below one at a time to install postgres locally.

```sh
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
sudo apt update
sudo apt install -y postgresql-client-13 libpq-dev

```


   - Next we run the docker compose up command to get postrges and dynamodb containers up & running.
   
   
   - Next we will update our gitpod.yml file with the postgres intallation command to automatically install when opened as shown below.
   
```sh
   - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
``` 

  - Next step:- We test if both services are installed and running smoothly as expected.

   - First we install the postrges extension on vscode called 'PostGreSQL' to enable us navigate through the service efficiently.
   
      - Once installed navigate to database explorer on the left pane on vscode click add connection to set up. 
      - Make sure to set password and click connect then type in the commands below and follow the prompt to test if postgres is configure and works perfectly. Remember to put the password you set when configuring the the connection in database explorer.


```sh
 psql -Upostgres --host localhost
``` 


### Below is a screenshot of what to expect if service is configured correctly
<img width="1195" alt="Screen Shot 2023-02-24 at 4 00 36 PM" src="https://user-images.githubusercontent.com/63635704/221198392-daf48673-3d05-4019-a21b-bcc66b2d027d.png">
 
 
     
### Next we write a flask backend enpoint for notifications





### Next we write a react page for notifications



