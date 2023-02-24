# Week 1 — App Containerization

In the second week of building the Cruddur micro-blogging app, we continued the process by configuring our backend with the installation of Flask and Python. This allowed us to start building out the server-side of our application, which will handle data storage, user authentication, and other critical functions.

We also started building out the frontend of the application using JavaScript and React, which required us to install npm to manage dependencies and build tools. This allowed us to start building out the user interface and user experience of the application.

To ensure that our application is scalable and easy to deploy, we decided to containerize it using Docker. We created a Docker Compose file with frontend, backend, Postgres, and DynamoDB commands, which allowed us to quickly spin up and test our application in a consistent and isolated environment.

Finally, we activated the notification settings on both the frontend and backend, which will allow our users to receive real-time updates and alerts about new posts and other activity on the platform.

Overall, we made significant progress in the second week of building the Cruddur micro-blogging app, and we are excited to continue developing and refining the application in the coming weeks.



# Tasks completed  in Week 1

First we 'cd' into the backend directory then run the following command which will install flask and map port 4567 to the backend which can be viewd on the browser.

```sh

python3 -m flask run --host=0.0.0.0 --port=4567

```

# We have two options to perform the next task

- First we create a ' docker-compose.yml ' file then define all dependencies & commands for both frontend and backend then build and run 
- secondly we can create a dockerfile for each (frontend and backend) define dependencies then create a network. 
- Next build the docker images then run the containers with the docker network and assign a port to the container to enable tcp connection or https access. 

```sh
docker run --name <container_name> --network container_network -d <container_name>
docker run --name <container_name> --network container_network -d -p <****:****> <container_name>
```

I will choose the first option which is very efficient and less coding 
-create a docker-compose.yml filen in the root of the project

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

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
    
```

Before running the docker up command, first install the dependencies for the backend and frontend individually

#Backend
```sh
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
pip3 install -r requirements.txt
python3 -m flask run --host=0.0.0.0 --port=4567

```
#Frontend
```sh
cd frontend-react-js
npm install

```

next we run the docker compose command, be sure to return to the root folder of the project before running the commands

```sh
docker compose up 

```

- make sure to unlock the port on the ports tab next to terminal in vscode
- Unlock the link for 4567 & 3000 to enable view in  browser
- append to the url to `/api/activities/home` for 4567 to view contents

<img width="1141" alt="Screen Shot 2023-02-24 at 2 21 34 PM" src="https://user-images.githubusercontent.com/63635704/221177949-0f6d7876-1b5e-42f8-a2ab-ab50c85b6349.png">
<img width="994" alt="Screen Shot 2023-02-24 at 2 22 38 PM" src="https://user-images.githubusercontent.com/63635704/221178232-c74723f3-796d-4739-8577-470e948e8778.png">

<img width="1304" alt="Screen Shot 2023-02-24 at 3 14 26 PM" src="https://user-images.githubusercontent.com/63635704/221187961-38735ce5-f659-418d-8eb9-39c8c3e5d526.png">
<img width="692" alt="Screen Shot 2023-02-24 at 3 15 33 PM" src="https://user-images.githubusercontent.com/63635704/221187970-2d6ac1b6-68a7-43da-8204-7d3a74827d40.png">
<img width="1222" alt="Screen Shot 2023-02-24 at 3 12 56 PM" src="https://user-images.githubusercontent.com/63635704/221188018-da66f458-ce1a-4bd9-a6e6-04d9616499e1.png">

