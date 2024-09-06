# Highly-Available-and-Scalable-Web-Application-Deployment-on-AWS
<br><br>
![Screenshot 2024-09-06 133858](https://github.com/user-attachments/assets/c77b768a-54e1-439f-b2a4-620856edb9d1)
<br><br>

# Create a VPC
Amazon Virtual Private Cloud (Amazon VPC) allows you to start AWS resources with a user-defined virtual network. This virtual network, along with the benefits of using AWS's scalable infrastructure, is very similar to the existing network operating in the customer's own data center.

## Create VPC through VPC Wizard
1. On the AWS console, select VPC from the service menu. Open it in a new tab.

2. Select VPC Dashboard and click Create VPC to create your own VPC.

3. To create a space to provision AWS resources used in this lab, we will create a VPC and Subnets. Select VPC and more in Resource to create a tab and change the name tag to Project-VPC-Lab. Leave the default setting for IPv4 CIDR block.

4. To design high availability architecture, we create 2 subnet space and select 2a and 2b for Customize AZs. And set the CIDR value of the public subnet that can communicate directly with the Internet as shown in the screen below. Set the CIDR value of the private subnet as

5. You can use a NAT gateway so that instances in your private subnets can connect to services outside your VPC, but external services cannot initiate direct connections to these instances. In this lab, we will create a NAT gateway in only one Availability Zone to save cost. Also, for DNS options, enable both DNS hostnames and DNS resolution. After confirming the setting value, click the Create VPC button.

6. As the VPC is created, you can see the process of creating network-related resources as shown in the screen below. For NAT Gateway, provisioning may take longer compared to other resources.



<br><br>
# Launch a web server instance
Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides secure, resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers. Amazon EC2’s simple web service interface allows you to obtain and configure capacity with minimal friction. It provides you with complete control of your computing resources and lets you run on Amazon’s proven computing environment.
<br><br>
## Launch instance and connect to web service

- First, let's create our Web Server instance. We will launch an Amazon Linux 2 instance, bootstrap Apache/PHP, and install a basic web page that will display information about our instance. Click on EC2 Dashboard near the top of the leftmost menu. And Click on Launch instances.

1. In Name, put the value Web server. And check the default setting for Amazon Machine Image below.

2. Select t2.micro in Instance Type.

3. Select the Proceed without a key pair (Not recommended).

4. Click the Edit button in Network settings to set the space where EC2 will be located.

6. Choose the VPC we created previously, and for the subnet, choose the public subnet. Auto-assign public IP is set to Enable.

7. Right below it, create Security groups to act as a network firewall. Security groups will specify the protocols and addresses you want to allow in your firewall policy. For the security group you are currently creating, this is the rule that applies to the EC2 that will be created. After entering Project - Web Server in Security group name and Description, select Add Security group rule and set HTTP to Type.
Select my IP for both HTTP and SSH traffic as the Source type.

8. All other values accept the default values, expand by clicking on the Advanced Details tab at the bottom of the screen.

9. Enter the following values in the User data field and select Launch instance.

10. Click the View Instances button in the lower right-hand portion of the screen to view the list of EC2 instances. Once your instance has launched, you will see your Web Server as well as the Availability Zone the instance is in, and the publicly routable DNS name. Click the checkbox next to your web server to view details about this EC2 instance.

11. Wait for the instance to pass the Status Checks to finish loading. Open a new browser tab and browse the Web Server by entering the EC2 instance’s Public DNS name into the browser. The EC2 instance’s Public DNS name can be found in the console by reviewing the Public IPv4 DNS name line highlighted above. You should see a website that looks like the following.

<br><br>
## Connect to your Linux instance using Session Manager

1. Session Manager is a fully managed AWS Systems Manager capability that lets you manage your Amazon EC2 instances through an interactive one-click browser-based shell or through the AWS CLI. You can use Session Manager to start a session with an instance in your account. After the session is started, you can run bash commands as you would through any other connection type.

2. On the AWS Management Console, search for the IAM console and open it in a new tab. In the navigation pane, choose Roles, and then choose Create role.

3. Under Select type of trusted entity, choose AWS service. Immediately under Choose the service that will use this role, choose EC2, and then choose Next.

4. On the Attach permissions policies page, do the following: Use the Search field to locate the AmazonSSMManagedInstanceCore. Select the box next to its name. Choose Next.

5. For Role name, enter a name for your new instance profile, such as SSMInstanceProfile. Choose Create role. The system returns you to the Roles page.

6. Go back Amazon EC2 console. In the navigation pane, under Instances, choose Instances. Choose your EC2 instance from the list and click Actions. In the Actions menu, choose Security, Modify IAM role.

7. For IAM role, select the instance profile you created SSMInstanceProfile. Then click on Update IAM role.
<br><br>
## Access the web service

1. In the EC2 instance console, select the instance you want to connect to, and then click the Connect button.

2. In the Connect to instance page, select Session Manager. Review the Session Manager usage section for the advantages of using Session Manager. Choose Connect. A new session will be started in a new tab. After the session is started, you can run bash commands as you would through any other connection type.

## Create a custom AMI

- In the AWS EC2 console, you can create a Custom AMI to meet your needs. This can then be used for future EC2 instance creation. On this page, let's create an AMI using the web server instance that we built earlier.

1. In the EC2 console, select the instance that we made earlier in this lab, and click Actions > Image and templates > Create Image.

2. In the Create Image console, type as shown below and press Create image to create the custom image.

3. Verify in the console that the image creation request is completed. In the left navigation panel, Click the AMIs button located under IMAGES. You can see the Status of the AMI that you just created. It will show either Pending or Available.

<br><br>
# Deploy auto-scaling web service

- We will deploy a web service that can automatically scale out/in under load and ensure high availability. We use the web server AMI created in the previous chapter and the network infrastructure named Project-VPC-Lab.

<br><br>
## Configure Application Load Balancer

1. From the EC2 Management Console in the left navigation panel, click Load Balancers under Load Balancing. Then click Create Load Balancer. In the Select load balancer type, click the Create button under Application Load Balancer.

2. Name the load balancer. In this case, the name is Web-ALB. Leave the other settings at their default values.

3. Scrolling down a little bit, there is a section for selecting availability zones. First, Select our vpc created previously. For Availability Zones select the 2 public subnets that were created previously.

4. In the Security groups section, click the Create new security group hyperlink. Enter Web-ALB-SG as the security group name and check the VPC information. Click the Add rule button and select HTTP as the Type and Anywhere-IPv4 as the Source. And create a security group.

5. Return to the load balancer page again, click the refresh button, and select the Web-ALB-SG you just created. Remove the default security group.

6. In the Listeners and routing column, click Create target group. Put Web-TG for the Target group name and check all settings same with the screenshot below. After that click Next button.

7. This is where we would register our instances. However, there are no instances to register at this moment. Click Create target group.

8. Again, move into the Load balancers page, click the refresh button, and select Web-TG. And then Click Create load balancer.

<br><br>
## configure launch template
Now that ALB has been created, it's time to place the instances behind the load balancer. To configure an Amazon EC2 instance to start with Auto Scaling Group, we will use the Launch Template to create an Auto Scaling group.

1. From the left navigation panel of the EC2 console, select Security Groups under the Network & Security heading and click Create Security Group in the upper right corner.

2. Scroll down to modify the Inbound rules. First, select the Add rule button to add the Inbound rules, and select HTTP in the Type. For Source, type ALB in the search bar to search for the security group created earlier Web-ALB-SG. This will configure the security group to only receive HTTP traffic coming from ALB.

3. Leave outbound rules' default settings and click Create Security Group to create a new security group. This creates a security group that allows traffic only for HTTP connections (TCP 80) that enter the instance via ALB from the Internet.


<br><br>
## Create a launch template

1. In the EC2 console, select Launch Templates from the left navigation panel. Then click Create Launch Template.

2. Let's proceed with setting up the launch template step by step. First, set the Launch template name and Template version description as shown below, and select Checkbox for Provide guidance in Auto Scaling guidance. Select this checkbox to enable the template you create to be utilized by Amazon EC2 Auto Scaling.

3. Scroll down to set the launch template contents. In Amazon Machine Image(AMI), set the AMI to Web Server v1, which was created in the previous EC2 lab. You can find it by typing Web Server v1 in the search section, or you can scroll down to find it in the My AMI section. Next, select t2.micro for the instance type. We are not going to configure SSH access because this is only for the Web service server. Therefore, we do not use key pairs.

4. Leave the other parts as default. Let's take a look at the Network Settings section. First, in Networking platform select Virtual Private Cloud(VPC). In the security group section, find and apply ASG-Web-Inst-SG created before.

5. Follow the Storage's default values without any additional change. Go down and define the Instance tags. Click Add tag and Name for Key and Web Instance for Value. Select Resource types as Instances and Volumes.

6. Finally, in the Advanced details tab, set the IAM instance profile to SSMInstanceProfile. Leave all other settings as default, and click the Create launch template button at the bottom right to create a launch template.

7. After checking the values set in Summary on the right, click Create launch template to create a template.

<br><br>
## Set Auto Scaling Group
1. Enter the EC2 console and select Auto Scaling Groups at the bottom of the left navigation panel. Then click the Create Auto Scaling group button to create an Auto Scaling Group.

2. In [Step 1: Choose launch template or configuration], specify the name of the Auto Scaling group. In this project, we will designate it as Web-ASG. Then select the launch template that you just created named Web. The default settings for the launch template will be displayed. Confirm and click the lower right Next button.


3. Set the network configuration with the Purging options and instance types as default. Choose Project-Lab-vpc for VPC, select Private subnet 1 and Private subnet 2 for Subnets. When the setup is completed, click the Next button.
  

4. Next, proceed to set up load balancing. First, select Attach to an existing load balancer. Then in Choose a target group for your load balancer, select Web-TG created during ALB creation. At the Monitoring, select checkbox for Enable group metrics collection within CloudWatch. This allows CloudWatch to see the group metrics that can determine the status of Auto Scaling groups. Click the Next button at the bottom right.

5. In the step of Configure group size and scaling policies, set the scaling policy for Auto Scaling Group. In the Group size column, specify Desired capacity and Minimum capacity as 2 and Maximum capacity as 4. Keep the number of instances to 2 as usual, and allow scaling of at least 2 and up to 4 depending on the policy.


6. In the Scaling policies section, select Target tracking scaling policy and type 30 in Target value. This is a scaling policy for adjusting the number of instances based on the CPU average utilization remaining at 30% overall. Leave all other settings as default and click the Next button in the lower right corner.

7. We will not Add notifications. Click the Next button to move to the next step. In the Add tags step, we will simply assign a name tag. Click Add tag, type Name in Key, ASG-Web-Instance in Value, and then click Next.


8. Now we are in the final stage of review. After checking the all settings, click the Create Auto Scaling Group button at the bottom right.

9. The Auto Scaling group has been created. You can see the Auto Scaling group created in the Auto Scaling group console as shown below.

10. Instances created through the Auto Scaling group can also be viewed from the EC2 Instance menu.

<br><br>
# Check web service and test

1. To access the Application Load Balancer configured for the web service, click the Load Balancers menu in the EC2 console and select the Web-ALB you created earlier. Copy DNS name from the basic configuration.


2. Open a new tab in your web browser and paste the copied DNS name. You can see that web service is working as shown below. In the figure below, you can see that the web instance is running this web page.


3. If you click the refresh button here, you can see that the host serving the web page has been replaced with an instance of another availability zone area as shown below. This is because routing algorithms in ALB target groups behave Round Robin by default.


4. Now, let's test load to see whether Auto Scaling works well. On your EC2 instance, enter the commands shown below. We are going to set option -c with 4 workers and -v to enable verbose logging. After some time, the CPU Utilization has spiked past 30%.

Run this command multiple times, the idea is to apply a reasonable amount of stress in order to make this work. Press CTRL^C to back out and reapply an additional stress command as needed.

5. Wait for about 5 minutes (300 seconds) and click the Activity tab to see the additional EC2 instances deployed according to the scaling policy.

6. When you click on the Instance management tab, you can see that two additional instances have sprung up and a total of four are up and running.

7. If you use the ALB DNS that you copied earlier to access and refresh the web page, you can see that it is hosting the web page in two instances that were not there before.

 <br><br>

