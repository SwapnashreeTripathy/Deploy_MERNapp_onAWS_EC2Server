# Deploy_MERNAPP_OnAWS_EC2Server
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2FSwapnashreeTripathy%2FDeploy_MERNapp_onAWS_EC2Server%2Fblob%2Fmain%2FREADME.md&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

# Project Objective

This Application is built on the backend with Nodejs and frontend with React technology, capable of handling around 1000 users providing feedback on their Travel Experiences details. The data is saved into a MongoDB cloud cluster. Two different EC2 servers host both the Frontend and Backend separately. 
Here's the architecture and deployment guideline:
Please refer to "https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/blob/main/Screenshots.md" to find detailed screenshot of setp by setp process.

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
9. Now write a Size of how much Storage/EBS volume you want. I have given 20GB. Lastly Click on “Launch Instances”.<br>
          voilàààààààà!!!!!! You have successfully created your EC2 instances.<br>

### Configure Nodejs Application on EC2 Server

1. Clone the Nodejs Application code in both the servers.<br>
   ```
   “Git clone https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server.git”
   ```
   
3. Rename 2 servers, one as “MERNApp_Frontend” & the other as “MERNApp_Backend” to refer to which Server is for the Backend code to run & which server is for the Frontend.

4. Install Node.js version 18 in both servers as our Application is written on Node.js version 18.
      ```
   sudo apt update
      ```
   
- Move to `~/Deploy_MERNapp_onAWS_EC2Server.git/frontend` folder in Frontend server & `~/Deploy_MERNapp_onAWS_EC2Server.git/backend` folder in backend server.
- Install “Node.js version 18” in both of the Instances.
  ```
  curl -s https://deb.nodesource.com/setup_18.x | sudo bash
  sudo apt install nodejs -y
  node -v
  ```
- Install all the dependent Libraries from for Frontend & Backend code in their respective environment.<br>
      after installation a folder “node_modules” will be created which will have all the dependent packages/libraries
  ```
  “sudo npm install”
  “npm  -v”
  ``` 


4. Establish a Connection between the Backend Server and MongoDB. In the Backend server, configure MongoDB details in the .env file to establish a connection between the backend server and MongoDB.
- Go to Mongo Compass & Click on your MongoDB Cloud server, then click on “Copy connection string”.
- Open `.env` file:
  ```
  sudo nano .env
  ```
  Add the following:
  ```
  MONGO_URI='ENTER_YOUR_MongoDB_Server_URL'
  PORT=3000
  ```

  ![image](https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/assets/139486876/d47be31c-68cb-4e0b-b0db-d55be8f4af54)


### Create 2 Load Balancers and attach those to Frontend & Backend Server

Use the Load Balancer to distribute the incoming load between 2 Frontend servers coming from users. Follow these steps:<br>
As we have created a Single Instance of the Frontend and backend Server, we need to “clone the server” so that we can “distribute the load using Load balancer” between those servers.

1. First Create an Image of the current Frontend server.
- To Create an AMI-->select Frontend Instance-->Click on Image and Templates-->Create Image. Give an Image Name & click on Create Image.

2. Then select this Image, click on “Launch Instance from AMI” and follow the same step for Creating an EC2 Instance.

3. Now Create an Application Load Balancer.

- Click on Load balancer tab-->Click on “Create Application Load Balancer”-->Fill Load Balancer Name-->Double-check if the VPC showing here is the same where you’ve EC2 instances.
- Select all Availability zones under MAPPING.
- Select your Security Group same as your Instances.
- Under Listeners & routing, Keep the Port as 80 (as Target Group will try to connect to the instances & check the health of the server).
- Click on Create Target group--> Choose Target Type as “Instances”-->provide a Target Name & click on Next-->Select your Available Instance-->Click on Include as pending below-->Create target group.<br>
  
  Now your Target group has been created. You can view the health of the server under “Target Group”. If the Target group is able to connect to the attached Instance the Health status will show as “Healthy”, else it will show as “unhealthy”. <br>
      The Target group sends requests to only the Instances whose status shows as “Healthy”.<br>
      Note – If you find Target Group as “Unhealthy” then Start your Instances/Server.<br>
      If you want to add any new Instances to an existing Load Balancer, then you need to “Register the Instance/Target” in Target Load.<br>

4. You will be back to the load balancer creation page & the target group we have created will be selected under “Listener & Routing”.

5. Lastly click on “Create Load balancer”.



### Configure Nginx and SSL on your EC2 backend & frontend Server

1. Install Nginx on your Servers.
```
   sudo apt update
   sudo apt install nginx
   sudo nginx -v
```

After installing it, in your browser localhost, You should see the default Nginx page.

2. Use "certbot" to download an SSL certificate.

Check if Nginx is installed or not:

![image](https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/assets/139486876/1b567df4-38ab-4375-abac-b32a810adcc4)

As we have installed Nginx, follow the steps below to configure SSL.

Open “certbot ” and select your webserver & on which OS it is running. & follow the bwlo link to get SSL installed commands.<br>

Before Running the commands to install SSL certificate you need to provide you domain name in /etc/nginx/sites-available/default file. So that Security will get provided to this domain.<br>
```
cd /etc/nginx/sites-available
cp default  default_26092023
nano default  -- provide your Domain name beside to “server_name”
```
For Frontend:
 ![image](https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/assets/139486876/a33365a4-676d-4205-942b-9e145249625f)

For Backend:
![image](https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/assets/139486876/51cdda38-6ffd-4bf4-9da3-f4d7374e9fd2)


```
“sudo apt update”
“sudo apt install snapd”

```

* To test your system, install the hello-world snap and make sure it runs correctly:
  ```
      "sudo snap install hello-world”
      “hello-world”

  ```

* If you have any Certbot packages installed, then need to remove it by below command.
  ```
  “sudo apt-get remove certbot”
  ```

```
“sudo snap install --classic certbot”
```

```
“sudo ln -s /snap/bin/certbot /usr/bin/certbot”
“sudo certbot --nginx” 
```
* After running this command it will ask you to Choose number Number defined for the Domain name. As for our Domain name number is “1”. Give 1 .
  

* To verify if Certbot installed or not, you can go to “/etc/nginx/sites-available/default” file and you will find that certbot has been installed.<br>
## Configure Reverse Proxy in BackendServer
•	As our backend runs on 3000 port, we will be setting up Reverse Proxy, to make the HTTP request go from port 80 to 3000.<br>
•	Provide below configuration in your /etc/nginx/sites-available/default file.<br>
      ![image](https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/assets/139486876/bf0faf8d-60ee-4340-9100-2d5ca79afb73)
## Domain Name Configuration on Cloudflare 
1. In CloudFlare first set up your Domain name, by searching your Domain name, to get NameServers from Cloud Falre.<br>
   

2. Copy the name servers and set them up in your DNS provider account. For example I have bought the Domain name from Godaddy, so I have configured these Nameservers in Godaddy.


3. In CloudFlare, under the DNS setting click on Add records to create  “CNAME” type Record. Provide your “DNS name” of the frontend load balancer at the respective  “frontend server Domain name”.

## Run the Frontend & Backend Server & try to access the Node JS Application
1.	Now Start both the Backend servers by going in the folder where your Nodejs Backend code is present.
   ```
"cd \backend"
“sudo node index.js”

```
![image](https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/assets/139486876/821daf73-7691-4f4f-adfa-6b1b71d537aa)
2.	Start the Frontend Server by going in the folder where your Nodejs Frontend code is present.
```
cd  “\Frontend”
“sudo npm start”
```
3.	Now go to browser, type your domain name such as “Swapnashree.live”. Once the page is up click on “Add Experince” tab, fill all the fields & Submit.
4.	Now connect to you Mongo Compass to check if the added data have been pushed to Mongo db or not
![image](https://github.com/SwapnashreeTripathy/Deploy_MERNapp_onAWS_EC2Server/assets/139486876/08f88e4a-f6d2-4098-8526-f0737d1093c6)


## *Welcome to my DevOps deployment project on GitHub! Thank you for dropping by!*

