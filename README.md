# Deploy_MERNAPP_OnAWS_EC2Server
# Project Objective

This Application is built on the backend with Nodejs and frontend with React technology, capable of handling around 1000 users providing feedback on their Travel Experiences details. The data is saved into a MongoDB cloud cluster. Two different EC2 servers host both the Frontend and Backend separately. Here's the architecture and deployment guideline:

## Architecture of the Project

1. Create 2 EC2 Instances where Frontend and Backend Codes run separately in each server.
2. Install Nginx on both servers.
3. Download an SSL certificate to your Server (Frontend or Backend) to make the Server protected (HTTPS) while users try to access it via HTTP request.
4. Configure Reverse Proxy in your Nginx Server (Frontend or Backend) as our Nodejs Application runs on Port number 3000, Nginx will help forward incoming HTTP requests to the application.
5. Create a Cloud Cluster on MongoDB and connect it through Mongo Compass to view the updated Documents.
6. Configure this MongoDB server in your Backend server to establish a connection between the Backend and MongoDB Server.
7. Try to Run your Web Application with the IP address of Frontend and Backend server to check If the setup was done properly till now. Add values to your Travel-Experience app to verify the added inputs are being saved into your MongoDB Database as Documents.
8. As our application is up and running fine while storing data in DB as well.
9. now let’s add a Load balancer to our Fronted Server. So that the incoming traffic will get well distributed among servers which will help to Improve overall performance by Preventing overload on 1 instance, & enhances Application Availability.
10. We need to create a Replica of our Fronted Server by Creating an AMI of it & then launch the Instance using this AMI.
## Diagram 

![image](https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/assets/139486876/ca255cf4-b3e2-4908-854e-9f88c7958755)



## Deployment Guideline

### Create & Setup EC2 instances

1. Login to your AWS account & select the region where you want to have your EC2 server created.
2. Give the Name of the Instance and give the Number of Instances as 2 (as we will have the same configuration type 2 Instances in the same VPC).
3. Select any AMI of the OS you want in both your Instances, here I am using Ubuntu Image.
4. Then choose any Instance type, am using “T2.micro” Free tier Instance.
5. For secure connection to AWS Instances, you need to create an Key-Pair.

      Click on “Create New Key pair”-->Give a Name-->Select Key-Pair type as RSA-->click on any Private key format (Use “.pem” to connect via “SSH” or Use “.pek” to connect via “Putty”)-->Click     on “Create Key Pair”.
      The Private key File will get downloadedsave it carefully as this file will help us to connect to EC2 Instances.

7. Next in “Security Group”, for Inbound Security group select IP address as “0.0.0.0/0=anywhere” so that Anyone can connect to this application using any router IP address.
       Select “HTTP, HTTPS, SSH” type protocol so that users can connect to this application via these protocol.
9. Now write a Size of how much Storage/EBS volume you want. I have given 20GB. Lastly Click on “Launch Instances”. & voilàààààààà!!!!!! You have successfully created your EC2 instances.

### Configure Nodejs Application on EC2 Server

1. Clone the Nodejs Application code in both the servers.
