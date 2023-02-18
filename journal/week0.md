# Week 0 — Billing and Architecture

As a new participant of the Free AWS Cloud Project Bootcamp, I am thrilled to share my first week's experience. During the course of the week, I got acquainted with a wide range of tools and technologies used in the Cloud Development Environment (CDE) namely GITPOD. GitPod is a web-based IDE that helps in streamlining the development process by providing a pre-configured environment for coding. Later, we delved deeper into the AWS ecosystem and learned about various AWS services such as EventBridge, SNS, IAM, AWS Budget, and Billing which knowledge we applied for the first weeks' task.

I spent a good number of hours watch the bootcamp vidoe while doing extensive research on the tools and services required to complete the task. I gained  hands-on practical experience. 

I went further by completing the Homework stretch Assignments and a bit more to gain real-work scenerio experience.


✅ Created and Generated AWS credentials for Iam admin user. I also assigned admin acces while i destroy my root account(joking LOL)
✅ Setup MFA for Root and admin Account.
✅ Used CloudShell in AWS console.
✅ I configured AWS CLI in Gitpod and added AWS Access Keys and secret Access key to env variables while i stored them in gitpod using gp command. I added code to the gitpod.yml file to enable all my cinfiguration load automatically when i log in.

✅ I Configured 2 AWS Budget using Gitpod CLI and set a threshold which will send notification when exceeded

✅ I used the command line in Gitpod to create a Budget and a Billing Alarm
✅ I Created CI/CD Lucid Logical Architectual Diagram and conceptual Design Diagram on area Napkin

✅ I Create a cloudwatch Alarm via AWS CLI 

✅ I used EventBridge to hookup a Health Dashboard to SNS which will send notification when there is a health issue

✅ I Opened a  ticket and requested support from AWS support


                                              # WEEK 0 HOMEWORK #
                        
###Created and Generated AWS credentials for Iam admin user. I also assigned admin acces while i destroy my root account(joking LOL)
###Setup MFA for Root and admin Account.


<img width="1168" alt="Screen Shot 2023-02-18 at 2 06 04 PM" src="https://user-images.githubusercontent.com/63635704/219865194-1d5661a1-5758-4fab-9d5b-81e024339e20.png">

<img width="1152" alt="Screen Shot 2023-02-18 at 2 03 22 PM" src="https://user-images.githubusercontent.com/63635704/219865212-394af7f5-7838-4e5b-a85d-b0e3b313aa2d.png">


###Used CloudShell in AWS console.
<img width="1429" alt="Screen Shot 2023-02-18 at 2 20 27 PM" src="https://user-images.githubusercontent.com/63635704/219865520-97308e92-f334-42a9-a26c-d40058b6aa09.png">



###I configured AWS CLI in Gitpod and added AWS Access Keys and secret Access key to env variables while i stored them in gitpod using gp command. I added code to the gitpod.yml file to enable all my cinfiguration load automatically when i log in.

<img width="711" alt="Screen Shot 2023-02-18 at 2 37 15 PM" src="https://user-images.githubusercontent.com/63635704/219866206-9f734936-55e8-4b04-bfda-be4b0b14be85.png">



### I Configured 2 AWS Budget using Gitpod CLI ans set a threshold which will send notification when exceeded

<img width="1440" alt="Screen Shot 2023-02-17 at 4 05 33 PM" src="https://user-images.githubusercontent.com/63635704/219866539-f9f2ac96-aeca-4c71-ba64-6e313b4edb21.png">



###I used the command line in Gitpod to create a Budget and a Billing Alarm

<img width="1069" alt="Screen Shot 2023-02-18 at 7 36 24 AM" src="https://user-images.githubusercontent.com/63635704/219866650-ff79c5b3-b99c-4b23-8228-bb33a3ef0064.png">

<img width="1093" alt="Screen Shot 2023-02-18 at 8 33 05 AM" src="https://user-images.githubusercontent.com/63635704/219866657-40308228-b000-4e50-8a7e-cc640c873015.png">


<img width="1171" alt="Screen Shot 2023-02-18 at 2 52 17 PM" src="https://user-images.githubusercontent.com/63635704/219866972-6a65bc77-9844-4448-8f57-9d3500333462.png">



###I Created CI/CD Lucid Logical Architectual Diagram and conceptual Design Diagram on area Napkin

I designed a logical diagram using lucid  [Logical Architectural Diagram](https://lucid.app/lucidchart/14600cd0-b751-47b9-8c4c-addf8c50ee84/edit?viewport_loc=-164%2C-68%2C3038%2C1539%2C0_0&invitationId=inv_1dcdfd90-7157-4ba1-b587-0fc47b34c86c)



###I Create a cloudwatch Alarm via AWS CLI 
<img width="1372" alt="Screen Shot 2023-02-18 at 8 36 00 AM" src="https://user-images.githubusercontent.com/63635704/219867026-55b14449-2ad1-4a39-826e-95febfac584a.png">

<img width="1429" alt="Screen Shot 2023-02-18 at 8 39 03 AM" src="https://user-images.githubusercontent.com/63635704/219867036-11a5c9f0-9a62-4769-ad42-7d981560f5b3.png">



###I used EventBridge to hookup a Health Dashboard to SNS which will send notification when there is a health issue 

First i created an sns topic using aws cli in gitpod

<img width="1047" alt="Screen Shot 2023-02-18 at 12 27 35 PM" src="https://user-images.githubusercontent.com/63635704/219867127-2e6b3a02-c1ac-4b8c-b239-244918c2b3a0.png">

Then i created an eventbridge and configured it to hook a health dashboard to the sns topic i created which will send a notification when there is a health issue
<img width="602" alt="Screen Shot 2023-02-18 at 12 31 34 PM" src="https://user-images.githubusercontent.com/63635704/219867163-74da2374-f0e1-4dfe-80b1-fd3a2b95d08e.png">

<img width="1144" alt="Screen Shot 2023-02-18 at 12 32 21 PM" src="https://user-images.githubusercontent.com/63635704/219867274-1629d431-1828-49cb-98ac-b332d3d7ddf5.png">




###I Opened a  ticket and requested support from AWS support
<img width="1140" alt="Screen Shot 2023-02-17 at 11 57 52 AM" src="https://user-images.githubusercontent.com/63635704/219868019-2fb54b0d-27b2-49c8-ae52-ed3d4721a507.png">

They replied to my issue giving me solutions which I applied and worked perfectly
<img width="999" alt="Screen Shot 2023-02-18 at 3 18 39 PM" src="https://user-images.githubusercontent.com/63635704/219868119-af83dc2c-05c4-4d5d-90b7-a775ee2c3216.png">
