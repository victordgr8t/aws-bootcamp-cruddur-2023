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
