# Week 0 — Billing and Architecture


As a new participant of the Free AWS Cloud Project Bootcamp, I am thrilled to share my first week's experience. During the course of the week, I got acquainted with a wide range of tools and technologies used in the Cloud Development Environment (CDE) namely GITPOD. GitPod is a web-based IDE that helps in streamlining the development process by providing a pre-configured environment for coding. Later, we delved deeper into the AWS ecosystem and learned about various AWS services such as EventBridge, SNS, IAM, AWS Budget, and Billing which knowledge we applied for the first weeks' task.


# The TASK AHEAD - Building a Micro-Blogging Website called Cruddur 
Building a micro-blogging app involves several steps and decisions, from choosing the technology stack to defining the user experience and monetization strategy. 

### Here is a brief outline of the process required to build an ephemeral-first micro-blogging platform.
Define the scope and requirements of the app, including the target user, features, and functionality. Determine the monetization strategy, such as advertising, subscription, or in-app purchases.

- Choose the technology stack for the front-end and back-end development. Based on the prompt, the front-end will be developed using JS with React, while the back-end will be built with Python using Flask, and will be API-based only.
- Select the cloud technologies to host the app. Since AWS is mentioned in the prompt, consider using AWS Elastic Beanstalk to deploy and manage the application.
- Develop the app iteratively, keeping in mind the budget constraint and user validation. Continuously update the app with new features and functionality  based on user feedback.
- Ensure that the app meets all legal requirements, such as implementing age limits if necessary.
- Implement a content moderation system to ensure that user-generated content is appropriate for the target audience.
- Set up budget monitoring to track expenses and ensure that the project stays within the allocated budget.
- Encourage user engagement by implementing features such as user stories and feature requests.
- Prepare a technical report for investors, including an architecture diagram, budget allocation, and operational expenses.
- Consider using containers to run microservices, as they can provide greater scalability and flexibility.

Overall, building a micro-blogging app requires careful planning, development, and management to ensure a successful and profitable product.

### The architectural diagram
When looking at an architectural diagram of what we plan to build, it's important to consider AWS's well-architected framework, which provides guidance on building secure, high-performing, and efficient systems on the AWS platform. The framework is organized into five pillars: Operational Excellence, Security, Reliability, Performance Efficiency, and Cost Optimization, with Sustainability as an optional sixth pillar.

- Operational Excellence involves continuous improvement and automation of processes to support business objectives. It includes best practices such as incident management, monitoring, and logging.
- The Security Pillar focuses on protecting data and systems from potential threats. It includes controls such as access management, encryption, and network security.
- The Reliability Pillar aims to minimize the impact of disruptions and ensure that systems remain available and functional. It includes practices such as fault tolerance, disaster recovery, and self-healing.
- The Performance Efficiency Pillar aims to use resources effectively and optimize the performance of systems. It includes practices such as load balancing, caching, and auto-scaling.
- The Cost Optimization Pillar aims to reduce costs while maintaining or improving the level of service. It includes practices such as resource optimization, cost monitoring, and capacity planning.
- The Sustainability Pillar focuses on minimizing the environmental impact of systems and promoting sustainability through efficient resource use and waste reduction.

By considering these pillars in the design and implementation of our cruddur app, we can ensure that it is secure, reliable, performant, cost-effective, and environmentally responsible.



I spent a good number of hours watch the bootcamp vidoe while doing extensive research on the tools and services required to complete the task. I gained  hands-on practical experience. 



I went further by completing the Homework stretch Assignments and a bit more to gain real-work scenerio experience.



✅ I Created and Generated AWS credentials for Iam admin user. I also assigned admin acces while i destroy my root account(joking LOL)

✅ Setup MFA for Root and admin Account.

✅ Used CloudShell in AWS console.

✅ I configured AWS CLI in Gitpod and added AWS Access Keys and secret Access key to env variables while i stored them in gitpod using gp command. I added code to the gitpod.yml file to enable all my cinfiguration load automatically when i log in.

✅ I Configured 2 AWS Budget using Gitpod CLI and set a threshold which will send notification when exceeded

✅ I used the command line in Gitpod to create a Budget and a Billing Alarm

✅ I Created CI/CD Lucid Logical Architectual Diagram and conceptual Design Diagram on area Napkin

✅ I Created a cloudwatch Alarm via AWS CLI 

✅ I used EventBridge to hookup a Health Dashboard to SNS which will send notification when there is a health issue

✅ I Opened a ticket and requested support from AWS support




                                              WEEK 0 HOMEWORK ASSIGNMENTS 
                                              
                        
## I Created and Generated AWS credentials for IAM admin user. I also assigned admin access while i destroy my root account(joking LOL)
<img width="1168" alt="Screen Shot 2023-02-18 at 2 06 04 PM" src="https://user-images.githubusercontent.com/63635704/219865194-1d5661a1-5758-4fab-9d5b-81e024339e20.png">




## I Setup MFA for Root and admin Account.
<img width="1152" alt="Screen Shot 2023-02-18 at 2 03 22 PM" src="https://user-images.githubusercontent.com/63635704/219865212-394af7f5-7838-4e5b-a85d-b0e3b313aa2d.png">




## I used CloudShell in AWS console.
<img width="1429" alt="Screen Shot 2023-02-18 at 2 20 27 PM" src="https://user-images.githubusercontent.com/63635704/219865520-97308e92-f334-42a9-a26c-d40058b6aa09.png">




## I configured AWS CLI in Gitpod and added AWS Access Keys and secret Access key to env variables while i stored them in gitpod using gp command. I added code to the gitpod.yml file to enable all my cinfiguration load automatically when i log in.

<img width="711" alt="Screen Shot 2023-02-18 at 2 37 15 PM" src="https://user-images.githubusercontent.com/63635704/219866206-9f734936-55e8-4b04-bfda-be4b0b14be85.png">



## I Configured 2 AWS Budget using Gitpod CLI and set a threshold which will send notification when exceeded

<img width="1440" alt="Screen Shot 2023-02-17 at 4 05 33 PM" src="https://user-images.githubusercontent.com/63635704/219866539-f9f2ac96-aeca-4c71-ba64-6e313b4edb21.png">




## I used the command line in Gitpod to create a Budget and a Billing Alarm

<img width="1069" alt="Screen Shot 2023-02-18 at 7 36 24 AM" src="https://user-images.githubusercontent.com/63635704/219866650-ff79c5b3-b99c-4b23-8228-bb33a3ef0064.png">


<img width="1093" alt="Screen Shot 2023-02-18 at 8 33 05 AM" src="https://user-images.githubusercontent.com/63635704/219866657-40308228-b000-4e50-8a7e-cc640c873015.png">


<img width="1171" alt="Screen Shot 2023-02-18 at 2 52 17 PM" src="https://user-images.githubusercontent.com/63635704/219866972-6a65bc77-9844-4448-8f57-9d3500333462.png">




## I Created CI/CD Lucid Logical Architectual Diagram and conceptual Design Diagram on area Napkin

I designed a logical diagram using lucid  
<img width="1402" alt="Screen Shot 2023-02-18 at 3 05 23 PM" src="https://user-images.githubusercontent.com/63635704/219868645-01527b42-27b1-4b7a-ae65-759500475bf4.png">
[Logical Architectural Diagram](https://lucid.app/lucidchart/14600cd0-b751-47b9-8c4c-addf8c50ee84/edit?viewport_loc=-164%2C-68%2C3038%2C1539%2C0_0&invitationId=inv_1dcdfd90-7157-4ba1-b587-0fc47b34c86c)


### I designed on a napkin a conceptual diagram of the cruddur micro blogging app
<img width="1436" alt="Screen Shot 2023-02-18 at 3 55 51 PM" src="https://user-images.githubusercontent.com/63635704/219869699-5d5dac1e-1169-4a1a-adef-a4a6909d334a.png">




## I Created a cloudwatch Alarm via AWS CLI 
<img width="1372" alt="Screen Shot 2023-02-18 at 8 36 00 AM" src="https://user-images.githubusercontent.com/63635704/219867026-55b14449-2ad1-4a39-826e-95febfac584a.png">

<img width="1429" alt="Screen Shot 2023-02-18 at 8 39 03 AM" src="https://user-images.githubusercontent.com/63635704/219867036-11a5c9f0-9a62-4769-ad42-7d981560f5b3.png">




## I used EventBridge from the management console to hookup a Health Dashboard to SNS which will send notification when there is a health issue 

First I created an sns topic using AWS CLI in gitpod

<img width="1047" alt="Screen Shot 2023-02-18 at 12 27 35 PM" src="https://user-images.githubusercontent.com/63635704/219867127-2e6b3a02-c1ac-4b8c-b239-244918c2b3a0.png">

Then i created an eventbridge and configured it to hook a health dashboard to the sns topic i created which will send a notification when there is a health issue

<img width="602" alt="Screen Shot 2023-02-18 at 12 31 34 PM" src="https://user-images.githubusercontent.com/63635704/219867163-74da2374-f0e1-4dfe-80b1-fd3a2b95d08e.png">

<img width="1144" alt="Screen Shot 2023-02-18 at 12 32 21 PM" src="https://user-images.githubusercontent.com/63635704/219867274-1629d431-1828-49cb-98ac-b332d3d7ddf5.png">




## I Opened a  ticket and requested support from AWS support
<img width="1140" alt="Screen Shot 2023-02-17 at 11 57 52 AM" src="https://user-images.githubusercontent.com/63635704/219868019-2fb54b0d-27b2-49c8-ae52-ed3d4721a507.png">

They replied to my issue giving me solutions which I applied and worked perfectly
<img width="999" alt="Screen Shot 2023-02-18 at 3 18 39 PM" src="https://user-images.githubusercontent.com/63635704/219868119-af83dc2c-05c4-4d5d-90b7-a775ee2c3216.png">




