# 🛰️ Troubleshooting EC2 IP Changes: Static vs Dynamic Addresses in AWS

## 📘 Scenario Overview


As a **Cloud Support Engineer at Amazon Web Services (AWS)**, I received a support request from a Fortune 500 company facing a networking issue within their AWS infrastructure. The core concern revolved around an EC2 instance named **Public Instance**, which keeps changing its public IP address every time it is stopped and started again.

## Here is the Message

Hello Cloud Support!

*We are having issues with one of our EC2 instances. The IP changes every time we start and stop this instance called Public Instance. This causes everything to break since it needs a static IP address. We are not sure why the IP changes on this instance to a random IP every time. Can you please investigate? Attached is our architecture. Please let me know if you have any questions.

Thanks!
Bob, Cloud Admin*

---

## 🧩 Objective

This lab project is aimed to:

- Investigate the customer's environment and correct the issue.
- Explain the difference between static and dynamic IP addresses in AWS.
- Solve the issue using **Elastic IP (EIP)**.
- Summarize  What I have found and provide a recommendatiON for production to stabilize.

---
---


![alt text](<architecture diagram.png>)
Figure: Customer VPC architecture, which includes one public subnet and one EC2 instance


---

---

## ⚙️ Tools & Services Used

- **AWS EC2**
- **Amazon VPC**
- **Elastic IP (EIP)**
- **Amazon Linux 2 AMI**
- **Security Groups**
- **Key Pairs**

---

# TASK 1 : Investigate the customer's environment

 Bob has assigned his EC2 instance a Dynamic IP address thats why it keep on constantly changing when it is stopped and started again? 
 I will be testing this theory by launching an EC2 instance in this AWS lab environment. 

 Bob cannot leave his instance on because it is very expensive for him to do so, and he requires this IP address to be set at a static IP address or else it breaks his other resources attached to it.

 I intend creating an instance in his environment and test it myself!

## Here is the procudere to do so

    Follow this steps below to complete the creation of an Amazon EC2 instance:

    After login in your AWS, navigate to your EC2 tab and click on the instances and select launch an instance.

**Step 1: Choose an Amazon Machine Image (AMI):**

![alt text](<screenshots/Name  tags AMI instance type.png>)

    Select the first entry for *Amazon Linux 2 AMI (HVM)* (Optional)
    An AMI is a template that contains the OS and configuration of the EC2 instance.

**Step 2: Choose an Instance Type:**

    Select *t3.micro* and navigate to the bottom of the window and click the button Next: Configure Instance Details

**Step 3: Configure Instance Details:**
![alt text](<screenshots/Step 3 Configure Instance Details.png>)
    Network: Choose vpc-xxxxxxxx | Lab VPC
    Subnet: Choose subnet-xxxxxx | Public Subnet 1
    Auto-assign Public IP: Set to enable
    Leave everything else as default and select *Next: Add Storage* Add Storage in the bottom right corner.

**Step 4: Add Storage:** 

    Leave as default and navigate to the bottom right of the window and select *Next: Add Tags.*

**Step 5: Add Tags:**

    Select Add Tag and under Key enter Name and under Value enter test instance
    Navigate to the bottom right of the window and select Next:* Configure Security Group*

**Step 6: Configure Security Group:** 
![alt text](<screenshots/Step 6 Configure Security Group.png>)
    Under *Assign a security group*, select the *Select an existing security group* radio button and select the security group with the name *Linux Instance SG*. Then navigate to the bottom of the window and hit Review and Launch.

**Step 7: Review Instance Launch:**

Navigate to the bottom of the window and hit Launch.

A pop-up window asks you to select an existing key pair or create a new key pair.

    In the first drop down, keep Choose an existing key pair.
    In the second drop down, select the key pair vockey | RSA.
    Check the box underneath the second drop down. Once checked, select Launch Instances.

![alt text](<screenshots/Launch success.png>)

**Here are my 2 instances**

![alt text](<screenshots/My 2 instances.png>)


## 🔍 Investigation Summary

![alt text](<screenshots/Test instance Public and Private IP adresss.png>)

Selecting the checkbox of the test instance. At the bottom, selecting the Networking tab. In this tab, I observed and noted the Public IPv4 address and the Private IPv4 address. Once noted, I selected the Instance state drop-down button, and selected Stop instance. Once the Instance state changes had Stopped, observe the Public and Private IPv4 address.

![alt text](<screenshots/Stopped instance public private IP address.png>)

Now restarting the test instance and navigating to the top window and selecting the Instance state and Start instance. Wai for the instance to start running. Taking note of the Public and Private IPv4 addresses. I did notice between the public and private IP addresses when you stopped and started the EC2 instance? 

![alt text](<screenshots/Test instance New IP.png>)

Would you consider this the Public IP a static or dynamic IP address? What would you consider the Private IP address for the EC2 instance? Do you think we have replicated the Bob's issue?

**Findings**

After launching and configuring an EC2 instance in the customer’s architecture (VPC + Public Subnet + IGW), I observed the following:

- **Public IP Address**: Changes upon stopping and starting the instance. ✅ **Dynamic**
- **Private IP Address**: Remains consistent across reboots. ✅ **Static**

This confirmed the Bob's report. The EC2 instance was configured to auto-assign a public IP from Amazon's dynamic pool, which explains the inconsistency.


**# SOLUTION**

## 🛠️ Solution Implementation

To provide a permanent public IP address to the EC2 instance, I followed these steps:

1. **Allocated an Elastic IP (EIP)**:
   - EIP is a static, public IPv4 address designed for dynamic cloud computing.
   - It remains attached to your AWS account until you explicitly release it.

2. **Associated the EIP with the EC2 Instance**:
   - Through the EC2 Dashboard > Elastic IPs > Associate Elastic IP.
   - Bound the allocated EIP to the EC2 instance’s private IP.

3. **Verified the Results**:
   - Restarted the instance multiple times.
   - Observed that the public IP (now the EIP) remained the same. ✅

**Detailed process**


Now let's solve the Bob's issue. Bob needs a permanent Public IP address that doesn't change when he stops and restarts his instance. AWS does have a solution that allocates a persistent public IP address to an EC2 instance, called an Elastic IP (EIP).


From the EC2 dashboard, lets navigate to Network and Security on the left navigation and select Elastic IPs. Notice there are no EIPs. Lets create one by selecting the button Allocate Elastic IP address in the top right. Keeping everything as default and hit Allocate. Let's Take note of the EIP address

![alt text](<screenshots/Allocate IP address.png>)

Select the EIP you just created by selecting the checkbox. Now attach this permanent, public IP address to the dynamic instance by navigating to the top right and navigating to Actions and Associate Elastic IP address.

![alt text](<screenshots/Associating Elastic IP address.png>)
Leaving the resource type as Instance, and selecting test instance from the Choose an Instance drop down menu. Under Private IP address, select the empty box. The Private IP associated with that instance is selected. Click the Associate button.


![alt text](<screenshots/Elastic IP allocated successfully.png>)
Navigate back to the Instances page using the left navigation pane. Select the checkbox for the test instance and navigate to the Networking tab. Take note of the Public IPv4 address. Did you notice that the EIP address is now the Public IP address? Now stop and start the instance and observe the differences. What did you observe? Is this a static or dynamic IP address? Did you solve the customer's issue? Why or why not?


1. What did you observe? The public IP remain the same before and after restating the instance.

2. Is this a static or dynamic IP address? Static IP -I t remain the same whether on or off.

3.  Did you solve the customer's issue? Yes, Bob's issue is solved. Even though we create our instance and used it that how you convert a dynamic IP to static one.


---

## 📊 Observations

| Property            | Before EIP          | After EIP             |
|---------------------|---------------------|------------------------|
| Public IPv4 Address | Changes on reboot   | Remains persistent     |
| Private IPv4 Address| Always consistent   | Always consistent      |
| Downtime risk       | High                | Minimal (due to IP loss)|
| Production-ready    | ❌ No                | ✅ Yes                  |

---

## ✅ Conclusion

The customer’s issue was due to the use of **dynamically assigned public IPs**, which AWS recycles after each instance shutdown. This behavior is by design to optimize public IP pool usage.

By assigning an **Elastic IP**, the EC2 instance now retains a **persistent public IP**, ensuring system stability and eliminating external service disruptions. The solution is cost-effective and scalable, especially for production workloads requiring fixed endpoints.

---

## 📌 Key Takeaways

- **Dynamic Public IPs** change on every stop/start cycle unless an EIP is attached.
- **Private IPs** assigned within a subnet are retained throughout the lifecycle of the instance.
- **Elastic IPs** are the correct solution for scenarios requiring a permanent public IP on EC2.
- Always evaluate if a static IP is required for external integrations before deployment.

---

## 📂 Project Metadata

- **Project Type**: AWS Networking & Support
- **Focus Area**: IP Address Management, EC2, VPC
- **Role**: AWS Cloud Support Engineer

---

## 🧠 Author

Kelvin Mwangi  
Security Analyst. Network security. Cloud security.
GitHub: [MwangiKelvin1](https://github.com/MwangiKelvin1)

---

## 📎 Related Topics

- AWS EC2 IP Behavior
- Networking in AWS VPC
- Elastic IP Best Practices
- Cloud Support Scenarios