# Highly-Available-and-Scalable-Web-Application-Deployment-on-AWS
<br><br>
![Screenshot 2024-09-06 133858](https://github.com/user-attachments/assets/c77b768a-54e1-439f-b2a4-620856edb9d1)
<br><br>

# Introduction 
## Overview:
- This project focuses on designing and deploying a highly available and scalable web application using core AWS services such as EC2, VPC, IAM, ALB, and Auto Scaling.
The goal is to create an architecture that can handle traffic spikes, ensure minimal downtime, and dynamically adjust resources.

## Key Objectives:
- Utilize a Virtual Private Cloud (VPC) to isolate and secure the network.
- Implement EC2 instances with a custom Amazon Machine Image (AMI) to speed up deployment.
- Achieve load balancing with an Application Load Balancer (ALB).
- Enable auto-scaling for dynamic instance scaling.
- Integrate monitoring and security best practices throughout the process.

<br><br>

# Create VPC with Subnets, NAT Gateway, and AZs

- What is a VPC? Virtual Private Cloud (VPC) is a secure, isolated network within AWS.

## Creating Subnets:
- Public subnets for Load Balancers, NAT Gateway, and other internet-facing services.
- Private subnets for EC2 instances and RDS databases.

## NAT Gateway:
- Allows EC2 instances in private subnets to connect to the internet for software updates while remaining inaccessible from the public internet.

- Availability Zones (AZs): Use multiple AZs to ensure fault tolerance and high availability.

- Best Practices: Ensure proper routing between public and private subnets with Route Tables. 

<br><br>

![image](https://github.com/user-attachments/assets/07ed6cf4-2a2d-4e17-a2c1-af03805327be)

<br><br>

![image](https://github.com/user-attachments/assets/b02443b5-a03a-4104-9f1d-741020e3ed25)

<br><br>

# Launch Web Server Instances

## Launch EC2 Instances:
- Select appropriate instance types (e.g., t2.micro for development or m5.large for production).
- Use an Auto Scaling group for dynamic scalability.

## Security Group Setup:
- Define inbound and outbound traffic rules (e.g., allow HTTP, HTTPS).
- Use least privilege access for better security.

## Execute User Data:
- Automate configuration tasks such as installing web servers (Apache, Nginx) by using user data scripts.
- Example: #!/bin/bash for installing software during instance launch.
- Best Practices: Keep security groups and key pairs secure and regularly review them. 

<br><br>

![image](https://github.com/user-attachments/assets/9bd461a6-e0da-4245-bb43-bd6df90886a1)


<br><br>

# Create IAM Policy and IAM Role

## IAM Policies:
- Create policies that define permissions for EC2 instances to interact with other AWS services (e.g., S3, CloudWatch, RDS).
- Example: Provide access to S3 for file storage or CloudWatch for monitoring.

## IAM Roles:
- Define a role for EC2 instances to assume. Instead of embedding AWS credentials into applications, assign roles to instances to securely access services.
- Example: An EC2 instance can use an IAM role to securely retrieve files from S3.

- Best Practices: Follow the principle of least privilege, only granting permissions that are necessary for the task. 

<br><br>

![image](https://github.com/user-attachments/assets/799d95de-583a-46d5-9676-813b72d6d13a)

<br><br>

# Attach IAM Role to EC2 Instances

## Attaching the IAM Role:
- Once the IAM role is created, attach it to the EC2 instances for secure access to AWS resources.
- This allows instances to interact with AWS services without requiring stored credentials.

- Security Benefits: IAM roles improve security by automatically rotating credentials and avoiding hard-coded access keys.

- Best Practices: Regularly audit role policies and permissions to ensure they are still valid. 

<br><br>

![image](https://github.com/user-attachments/assets/c4fbf7a3-da8e-411a-a18a-e6fb48739862)

<br><br>

# Create a Custom Amazon Machine Image (AMI)

## What is an AMI?
- An AMI is a pre-configured operating system image, which includes all software, configurations, and updates required for your application.

## Creating an AMI:
- Customize your EC2 instance with your application, system configurations, and installed software.
- Use the "Create Image" option to save the instance as a reusable AMI.

## Advantages:
- Faster instance launches, as the AMI comes pre-configured with software and settings.
- Useful for Auto Scaling groups and rapid deployments.

- Best Practices: Regularly update your AMIs to include the latest patches and software versions. 

<br><br>

![image](https://github.com/user-attachments/assets/4be437b1-d2da-4e20-9721-3902b60bf567)

<br><br>

![image](https://github.com/user-attachments/assets/7cef7b2c-5406-4765-9828-9ea07cc3dff1)

<br><br>

![image](https://github.com/user-attachments/assets/158921e3-f92e-4dae-86e1-018564f061da)

<br><br>

# Create an Application Load Balancer (ALB)

## Application Load Balancer (ALB):
- The ALB distributes incoming traffic across multiple EC2 instances in different Availability Zones to ensure high availability and resilience.
- ALBs handle HTTP and HTTPS requests and are designed for advanced routing and target health checks.

## Security Group for ALB:
- Allow only inbound HTTP/HTTPS traffic.
- Ensure the security group is tightly controlled to prevent unauthorized access.

## Target Groups:
- Assign EC2 instances in private subnets to the target group, where the ALB routes traffic.
- Health checks ensure traffic is only routed to healthy instances.

- Best Practices: Use path-based routing for multiple services and always enable health checks for each target. 

<br><br>

![image](https://github.com/user-attachments/assets/b5ca862c-341f-4729-bed4-e7728c31dde5)

<br><br>

![image](https://github.com/user-attachments/assets/9ca1d2c9-4231-4d3e-aa83-d40b34dc63f3)

<br><br>

![image](https://github.com/user-attachments/assets/7a98b501-45f6-480b-98da-78f348cbb862)

<br><br>

# Create Auto Scaling Group Security Group

## Auto Scaling Group (ASG) Security:
- Define a security group for instances launched by the Auto Scaling group.
- Ensure only necessary ports (e.g., HTTP, HTTPS) are open to the ALB.

## Benefits of Auto Scaling: 
- Automatically increases or decreases the number of running instances based on demand.

## Security Best Practices:
- Limit inbound rules for Auto Scaling instances to minimize the attack surface.
- Use IAM roles to manage access for instances to communicate securely with other AWS services.

<br><br>

![image](https://github.com/user-attachments/assets/55f6e7b5-4fcd-4f8a-9d20-d487fd05df7c)

<br><br>

# Configure Launch Template with Custom AMI

## Launch Template:
- Configure a launch template with settings such as instance type, security groups, key pairs, and the custom AMI created earlier.

## Using Custom AMI:
- The custom AMI ensures that all instances launched by Auto Scaling come pre-configured with the necessary software and configurations.

## Launch Template Advantages: 
- Ensures consistency across instances and simplifies scaling by using pre-configured settings.

- Best Practices: Keep launch templates updated to reflect any changes in the AMI or instance configurations. 

<br><br>

![image](https://github.com/user-attachments/assets/6f74fdff-0d22-4def-b661-cbf043469cc6)

<br><br>

![image](https://github.com/user-attachments/assets/56421d1c-321a-40c2-8d08-2fefe5792e1b)

<br><br>

![image](https://github.com/user-attachments/assets/2cd24358-4c81-4c5f-8d0e-5849a5d7736b)

<br><br>

# Configure an Auto Scaling Group

## Auto Scaling Group Setup:
- Define scaling policies based on metrics like CPU utilization or network traffic.
- Scale-out policy: Add more instances when demand increases.
- Scale-in policy: Terminate instances when demand decreases to save costs.

## Availability Zones:
- Ensure the Auto Scaling group spans multiple AZs to distribute instances and prevent failure from impacting availability.

- Best Practices: Set conservative scaling policies initially, then adjust based on actual traffic patterns. 

<br><br>

# Test Auto-Scaling and Manual Settings

## Simulate Load Testing:
- Use stress testing tools like Apache JMeter or AWS Load Testing to generate high traffic and test the Auto Scaling policies.
- Ensure that instances are automatically added and removed as the traffic changes.

## Manual Scaling Adjustments:
- Modify scaling thresholds based on performance.
- Optimize scaling policies by adjusting cooldown periods or using predictive scaling features in AWS.

- Best Practices: Monitor scaling performance with CloudWatch metrics and alarms, ensuring that the application maintains high availability. 
<br><br>

![image](https://github.com/user-attachments/assets/3a5571e7-ab5b-4f4d-81b7-b8e3a3a322d3)

<br><br>

![image](https://github.com/user-attachments/assets/d1ffe76a-3a9a-45ae-ad01-9102341c3f4c)

<br><br>

![image](https://github.com/user-attachments/assets/d1ae5b77-60de-407c-8158-5f9271c3b7c7)

<br><br>

# Conclusion

## Project Summary:
- Successfully deployed a multi-tier web application using AWS services that ensures high availability, scalability, and fault tolerance.
Implemented core AWS services like VPC, EC2, IAM, ALB, and Auto Scaling to build a robust infrastructure.

## Key Benefits:
- High Availability: The application is distributed across multiple Availability Zones, ensuring minimal downtime.
- Scalability: Auto Scaling dynamically adjusts the number of instances based on traffic demands, optimizing performance and cost.
- Security: IAM roles and security groups ensure the application and instances are securely managed.
- Resilience: With built-in fault tolerance through multi-AZ deployment and load balancing, the application can handle failure scenarios without service interruption.

<br><br>

# References and Resources

## AWS Documentation:
- Amazon EC2 - Elastic Compute Cloud documentation.
- Amazon VPC - Virtual Private Cloud documentation.
- Amazon IAM - Identity and Access Management.
- Amazon ALB - Application Load Balancer documentation.
- AWS Auto Scaling - Information on Auto Scaling features.

## GitHub Repository:
- Highly Available and Scalable Web Application on AWS - Source code for project implementation.

## Additional Resources:
- AWS Whitepapers: Architecting for the Cloud.
- AWS Training and Certification.



