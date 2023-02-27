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

- First we create a file called notifications_activities.py inside ./backend_flask/services folder and input code below.
```sh
from datetime import datetime, timedelta, timezone
class NotificationsActivities:
  def run():
    now = datetime.now(timezone.utc).astimezone()
    results = [{
      'uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
      'handle':  'Elon Musk',
      'message': 'Who let the Doge out!',
      'created_at': (now - timedelta(days=2)).isoformat(),
      'expires_at': (now + timedelta(days=5)).isoformat(),
      'likes_count': 5,
      'replies_count': 1,
      'reposts_count': 0,
      'replies': [{
        'uuid': '26e12864-1c26-5c3a-9658-97a10f8fea67',
        'reply_to_activity_uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
        'handle':  'Worf',
        'message': 'This post has no honor!',
        'likes_count': 0,
        'replies_count': 0,
        'reposts_count': 0,
        'created_at': (now - timedelta(days=2)).isoformat()
      }],
    },
    
    ]
    return results
```
- next we open app.py file and update code below

``` sh
@app.route("/api/activities/notifications", methods=['GET'])
def data_notifications():
  data = NotificationsActivities.run()
  return data, 200

```
<img width="1022" alt="Screen Shot 2023-02-24 at 7 01 24 PM" src="https://user-images.githubusercontent.com/63635704/221241534-d5f1d8b9-5834-442b-b13f-185d6657e36b.png">




### Next we update our frontend folder which is written in javascript & react page to correspond with the backend for notifications

- First update the app.js file by adding codes below 
```sh
import NotificationsFeedPage from './pages/NotificationsFeedPage';

path: "/notifications",
    element: <NotificationsFeedPage />

```



<img width="920" alt="Screen Shot 2023-02-24 at 7 12 02 PM" src="https://user-images.githubusercontent.com/63635704/221243794-23c32873-1f2a-4c23-9af1-284e615a8247.png">

- Under pages create  two files notificationsFeedPage.js & notificationsFeedPage.css 
```sh
import './NotificationsFeedPage.css';
import React from "react";

import DesktopNavigation  from '../components/DesktopNavigation';
import DesktopSidebar     from '../components/DesktopSidebar';
import ActivityFeed from '../components/ActivityFeed';
import ActivityForm from '../components/ActivityForm';
import ReplyForm from '../components/ReplyForm';

// [TODO] Authenication
import Cookies from 'js-cookie'

export default function NotificationsFeedPage() {
  const [activities, setActivities] = React.useState([]);
  const [popped, setPopped] = React.useState(false);
  const [poppedReply, setPoppedReply] = React.useState(false);
  const [replyActivity, setReplyActivity] = React.useState({});
  const [user, setUser] = React.useState(null);
  const dataFetchedRef = React.useRef(false);

  const loadData = async () => {
    try {
      const backend_url = `${process.env.REACT_APP_BACKEND_URL}/api/activities/notifications`
      const res = await fetch(backend_url, {
        method: "GET"
      });
      let resJson = await res.json();
      if (res.status === 200) {
        setActivities(resJson)
      } else {
        console.log(res)
      }
    } catch (err) {
      console.log(err);
    }
  };

  const checkAuth = async () => {
    console.log('checkAuth')
    // [TODO] Authenication
    if (Cookies.get('user.logged_in')) {
      setUser({
        display_name: Cookies.get('user.name'),
        handle: Cookies.get('user.username')
      })
    }
  };

  React.useEffect(()=>{
    //prevents double call
    if (dataFetchedRef.current) return;
    dataFetchedRef.current = true;

    loadData();
    checkAuth();
  }, [])

  return (
    <article>
      <DesktopNavigation user={user} active={'notifications'} setPopped={setPopped} />
      <div className='content'>
        <ActivityForm  
          popped={popped}
          setPopped={setPopped} 
          setActivities={setActivities} 
        />
        <ReplyForm 
          activity={replyActivity} 
          popped={poppedReply} 
          setPopped={setPoppedReply} 
          setActivities={setActivities} 
          activities={activities} 
        />
        <ActivityFeed 
          title="Notifications" 
          setReplyActivity={setReplyActivity} 
          setPopped={setPoppedReply} 
          activities={activities} 
        />
      </div>
      <DesktopSidebar user={user} />
    </article>
  );
}

```



- Next we run a docker compose up command to implement new changes.
  - We open all the ports fromm the port tab next to the terminal, then we click the url for port 3000 



### Final result - the image below shows that our micro-blogging app called cruddur is coming to life gradually :)

<img width="1141" alt="Screen Shot 2023-02-24 at 7 21 23 PM" src="https://user-images.githubusercontent.com/63635704/221248828-cc461750-5a3c-4b68-940c-cc3890d3698c.png">


### Click the notification button to get to the notifications page we just created.
<img width="1125" alt="Screen Shot 2023-02-24 at 7 31 46 PM" src="https://user-images.githubusercontent.com/63635704/221248856-78fb1c61-666f-4345-aca7-8a2bab70a91e.png">


# HOMEWORK CHALLENGES
### - Run the dockerfile CMD as an external script

  - To run the CMD specified in a Dockerfile as an external script, I created a new script file containing the command and then modify the Dockerfile to call the script instead of directly executing the command as shown below...

First created a docker-script.sh with command below
```
    #!/bin/bash
    echo "Hello World"

```
 Next I created a Dockerfile with command below. 
 ```
    FROM python:3.8-alpine
    COPY . /hello-python
    WORKDIR /hello-python
    CMD ["sh", "/hello-python/docker-script.sh"]

 ```
 I Modified my Dockerfile to call the script instead of directly executing the command.
 
   ``` 
       docker build -t app .
       docker run app
   ```
 
 Got the result below which display  'Hello World'
 <img width="741" alt="Screen Shot 2023-02-27 at 12 57 49 PM" src="https://user-images.githubusercontent.com/63635704/221546241-6526ed51-6856-4699-8049-32291833c58a.png">
 
 
 
### - Push and tag a image to DockerHub   

  -  To push an image to dockerhub first you need to create a repository on dockerhub
  -  next type on the terminal command  below
  
      ```  
          docker login username=your_username 
          docker tag app vicdg8t/app:v1.0 
          docker push vicdg8t/app:v1.0 
      ```
           
  I got the results below         
<img width="761" alt="Screen Shot 2023-02-27 at 1 12 05 PM" src="https://user-images.githubusercontent.com/63635704/221549623-e0f360b4-7f50-44a9-825b-88ac73a72a2e.png">

<img width="730" alt="Screen Shot 2023-02-27 at 1 13 49 PM" src="https://user-images.githubusercontent.com/63635704/221549629-b240367a-a1f5-4a3b-aa01-ea46f8b9beea.png">




### - Use multi-stage building for a Dockerfile build

Multi-stage builds allow you to use multiple 'FROM' statements in a single Dockerfile to create multiple temporary images, each with their own set of instructions, before creating a final image. The final image created is smaller which is the whole purpose of containers in the first place. 
 
 
   - first I created a Dockerfile  file with code below 
    
   ``` 
       # Build executable stage
      FROM golang:alpine AS build

      ADD . /app
      WORKDIR /app
      ADD main.go .
      RUN go build -o app

      # Build final image
      FROM alpine:latest
      RUN apk --no-cache add ca-certificates
      WORKDIR /root/
      COPY --from=build /app/app .

      CMD ["./app"]
   
   ```
   
   
   - next I created a ' go.mod and main.go' files respectively 
      
      ### main.go
      ```
          package main

          import "fmt"

          func main() {
            fmt.Println("Hello, world!")
          }

      ```
      
      ### go.mod
      
      ```
          module myapp

          go 1.16
      
      ```
     
       
       
   In the docker compose file I made reference to the dockerfile i created earlier.
   
   run 
      
      ```
          docker build -t app:v1.0 . 
          docker run app:v1.0 
      ```  
      
   to get the result below which is a simple hello world app I built using go programming language
   
   
   <img width="957" alt="Screen Shot 2023-02-27 at 2 50 57 PM" src="https://user-images.githubusercontent.com/63635704/221569820-2df9a13c-6003-4898-ba7d-0c035e4597dd.png">





### - Implement a healthcheck in the V3 Docker compose file

   From the app I created with go in the previous task, I implemented a healthcheck on the container which will run with 'docker-compose up' cmd.
   
   - first I have to create a docker-compose.yml file with code below
     
     ```
          version: '3'

          services:
            health-service:
              build:
                context: .
                dockerfile: Dockerfile
              ports:
                - "8080:8080"
              healthcheck:
                test: ["CMD", "curl", "-f", "http://localhost:2000/health"]
                interval: 1m
                timeout: 10s
                retries: 3
     
     ```
     
   I made a reference in the file above to the dockerfile i created ealier which will run a healthcheck when ``` docker-compose up ``` is run




### - Research best practices of Dockerfiles and attempt to implement it in your Dockerfile
 
   - Use official base images whenever possible: Official base images are well-maintained and have fewer vulnerabilities. They are also updated regularly, which ensures that your container has the latest security patches.
   
   - Keep your Dockerfile small and focused: Your Dockerfile should only include the necessary components to run your application. This helps keep the image size small and reduces the risk of vulnerabilities.
   
   - Use multi-stage builds: Multi-stage builds allow you to build your application in one stage and then copy only the necessary files to the final image. This helps keep the image size small and reduces the attack surface.
   
   - Use caching wisely: Caching can speed up the build process, but it can also cause issues if you don't use it properly. You should only cache layers that are unlikely to change frequently.
   
   -   Avoid running commands as root: Running commands as root in a container can pose a security risk. You should switch to a non-root user as soon as possible in your Dockerfile.
   
   - Use environment variables: Environment variables make it easy to configure your container without having to modify the Dockerfile. This can be especially useful if you want to use the same Dockerfile for different environments.
   
   - Add labels to your images: Labels provide metadata about your image, such as the version number and build date. This can be helpful for tracking and debugging issues.



### - Learn how to install Docker on your localmachine and get the same containers running outside of Gitpod / Codespaces

   - First i visited the docker website, 
   - then i downloaded the docker desktop for my operating system 
   - then i followed the installation wizard to install on my computer
   - Next i ran command below to confirm installation of docke ron my local machine
   
    
     ```
         systemctl enable docker.service 
         systemctl start docker.service 
         docker --version 
         
     ```  
     



### - Launch an EC2 instance that has docker installed, and pull a container to demonstrate you can run your own docker processes. 
 
 - First i launch an ec2 instance and passed user data with instructions to install wordpress and docker installed using terraform with command below
    i update permission for the ec2 user so as to grant admin access hence i wont use sudo command 
   
```
  provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "Wordpress_Instance" {
  ami           = "ami-01234567890"
  instance_type = "t2.micro"
  key_name      = "my-key-pair"
  subnet_id     = "subnet-123456789"
  vpc_security_group_ids = ["sg-123456789"]

  user_data = <<-EOF
              #!/bin/bash
              yum update -y
              amazon-linux-extras install docker -y
              service docker start
              pip3 install --user docker-compose
              usermod -a -G docker ec2-user
              docker run -p 80:80 -d wordpress
              EOF

  tags = {
    Name = "Wordpress_Instance"
  }
}

   ```
   
   Next i ssh'ed into the created ec2 instance using ec2 instance-connect then typed in commands below to pull the docker image I pushed to my repo 
   
   ``` 
      docker --version
      docker ps 
      systemctl status docker.service
      docker pull vicdg8t/app:v1.0
      docker images
   ```
   
   I got the results below

<img width="1287" alt="Screen Shot 2023-02-27 at 1 58 52 PM" src="https://user-images.githubusercontent.com/63635704/221558385-c5796176-f010-40e8-8bd1-105986dfcfae.png">




# CONTAINER SECURITY

In this task I will use synk to scan our containers for security vulnerabilities. 

### What does SYNK do? 
Snyk is a platform allowing you to scan, prioritize, and fix security vulnerabilities in your own code, open source dependencies, container images, and Infrastructure as Code (IaC) configurations.

I signed up on synk, then added my github account to synk by granting access to synk to enable synk scan my folders for security vulnerabilities.
Synk scanned my account and found some security vulnerabilities which it highlighted me to take immediate action. Find result below


<img width="1398" alt="Screen Shot 2023-02-27 at 7 00 09 PM" src="https://user-images.githubusercontent.com/63635704/221633708-df3b9452-0884-4b46-a2c4-91ca7dedc427.png">
