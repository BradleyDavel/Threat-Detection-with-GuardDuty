![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![Amazon EC2](https://img.shields.io/badge/Amazon%20EC2-FF9900?style=for-the-badge&logo=amazonec2&logoColor=white)
![Amazon S3](https://img.shields.io/badge/Amazon%20S3-569A31?style=for-the-badge&logo=amazons3&logoColor=white)
![Amazon VPC](https://img.shields.io/badge/Amazon%20VPC-146EB4?style=for-the-badge&logo=amazonaws&logoColor=white)
![Amazon CloudFront](https://img.shields.io/badge/Amazon%20CloudFront-FF4F8B?style=for-the-badge&logo=amazonaws&logoColor=white)
![Amazon GuardDuty](https://img.shields.io/badge/Amazon%20GuardDuty-DD344C?style=for-the-badge&logo=amazonaws&logoColor=white)
![CloudFormation](https://img.shields.io/badge/AWS%20CloudFormation-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Security](https://img.shields.io/badge/Security-GuardDuty%20Lab-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Project%20Status-Completed-success?style=for-the-badge)


<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Threat Detection with GuardDuty

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-guardduty)

**Author:** Bradley Davel  
**Email:** bradley.davel@outlook.com

---

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-security-guardduty_v1w2x3y4)

---

## Introducing Today's Project!

### Tools and concepts

The services I used were Amazon EC2, Amazon S3, Amazon VPC, AWS CloudFormation, Amazon CloudFront, and Amazon GuardDuty. Key concepts I learnt include how vulnerable applications can be exploited with SQL injection and command injection, how attackers can use stolen credentials to access sensitive data in S3, and how GuardDuty detects suspicious behavior such as credential misuse, data exfiltration, and malware uploads. I also gained hands-on experience with threat detection, malware protection, and simulating real-world attack techniques in a controlled AWS environment.

### Project reflection

This project took me approximately 30–40 minutes to complete. The most challenging part was carefully executing the SQL injection and command injection attacks without making mistakes in the payloads. It was most rewarding to see GuardDuty successfully detect both the credential misuse and the malware test file, proving that AWS security services can identify and flag real-world attack patterns.

I did this project today to practice threat detection with Amazon GuardDuty in a hands-on way. By deploying a purposely vulnerable app, performing attacks like SQL injection, credential theft, and malware upload, I was able to see how GuardDuty responds to real attack scenarios. This gave me practical experience in both offensive security techniques and defensive monitoring, which helps me strengthen my cloud security skills.

---

## Project Setup

To set up for this project, I deployed a CloudFormation template that launches a vulnerable web application environment. The template provisions three main components: web app infrastructure hosted on an EC2 instance inside a new VPC with networking resources like subnets, security groups, an internet gateway, route tables, endpoints, a load balancer, and auto scaling, plus a CloudFront distribution to deliver the app globally; an S3 bucket containing important-information.txt, simulating sensitive data that the EC2 instance can access; and GuardDuty, enabled for continuous threat detection and monitoring. This setup creates a realistic environment where I can simulate attacks, observe how credentials and sensitive data might be compromised, and validate that GuardDuty detects malicious activity.

This project is about threat detection with Amazon GuardDuty. I deployed the vulnerable web app using a CloudFormation template instead of setting everything up manually, so I could immediately focus on testing attacks and analyzing the results. By skipping manual setup, I can spend more time in GuardDuty, where I’ll investigate whether it detects credential theft, data breaches, and other suspicious activity generated during my simulated hacks.

GuardDuty is an intelligent threat detection service in AWS that continuously monitors your environment for malicious activity, unauthorized behavior, and potential security risks by analyzing logs, network traffic, and account activity. In this project, it will detect and report on attacks against my vulnerable web application, helping me see how AWS security services identify credential misuse, data theft attempts, and suspicious patterns in real time.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-security-guardduty_n1o2p3q4)

---

## SQL Injection

The first attack I performed on the web app is SQL injection, which means I inserted malicious SQL code into the login form in order to trick the application into bypassing authentication checks. By entering a query like ' or 1=1;--, I was able to manipulate the database into always returning a valid result, giving me admin access without knowing the correct credentials. SQL injection is a security risk because it allows attackers to bypass authentication, steal or modify sensitive data, and even take control of the underlying database, making it one of the most dangerous and common vulnerabilities in insecure web applications.

My SQL injection attack involved entering the payload ' or 1=1;-- into the email field of the login form and using any value for the password. This means the web app’s backend database interpreted my input as a logical statement that is always true (1=1), which bypassed the normal credential check. The -- sequence then commented out the rest of the SQL query, preventing errors and ensuring I was logged in as an admin without providing valid credentials.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-security-guardduty_h1i2j3k4)

---

## Command Injection

Next, I used command injection, which is when an attacker runs system-level commands on a vulnerable server by injecting malicious input into an application. The Juice Shop web app is vulnerable to this because it does not sanitize or validate user input before passing it into backend processes. By crafting a payload that called the EC2 instance metadata service, I was able to retrieve IAM role credentials directly from the server. This lack of sanitization means the app processes harmful commands as if they were legitimate input, allowing attackers to execute arbitrary code and escalate their access.

To run command injection, I entered a malicious payload into the username field of the web app’s profile page that executed system commands on the underlying EC2 instance. The script will call the EC2 instance metadata service at 169.254.169.254 to request temporary IAM role credentials, then save those credentials into a JSON file inside the app’s public assets directory so they can be accessed later. This works because the web app does not sanitize or validate input before passing it into backend processes, allowing my injected code to run directly on the server.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-security-guardduty_t3u4v5w6)

---

## Attack Verification

To verify the attack's success, I checked the credentials file that my command injection script created in the app’s public assets. The credentials page showed me an AccessKeyId, SecretAccessKey, and Session Token, which confirms I was able to steal temporary IAM role credentials from the EC2 instance metadata service. This proves that the command injection worked and gave me valid AWS credentials that I could now use to escalate the attack further.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-security-guardduty_x7y8z9a0)

---

## Using CloudShell for Advanced Attacks

The attack continues in CloudShell, because it provides a browser-based terminal with the AWS CLI already installed and configured to interact with my account. Since I’m simulating a hacker using stolen IAM credentials, CloudShell lets me quickly swap in those credentials and run the same commands an attacker would use to access private data. This makes it possible to test the real impact of a breach directly inside my AWS environment without needing extra setup.

In CloudShell, I used wget to download the credentials.json file that my earlier command injection attack had exposed through the web app’s public URL, giving me a local copy of the stolen AWS credentials. Next, I ran a command using cat and jq to pretty-print and inspect the JSON content of the file, which revealed sensitive fields like the AccessKeyId, SecretAccessKey, and Token. This step confirmed that I had successfully exfiltrated valid AWS credentials, which could then be used to authenticate against AWS services as if I were the compromised EC2 instance.

I then set up a profile, called stolen, to configure the AWS CLI with the Access Key, Secret Access Key, and Session Token I had stolen from the vulnerable web application. I had to create a new profile because my default CloudShell profile is tied to my legitimate IAM identity, but for this simulation I needed to act as the attacker using the compromised credentials. This separation allowed me to run AWS commands as the hacker and later analyze whether GuardDuty detected the suspicious activity.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-security-guardduty_j9k0l1m2)

---

## GuardDuty's Findings

After performing the attack, GuardDuty reported a finding within about 15 minutes. Findings are not generated instantly because GuardDuty analyzes logs and activity patterns in the background before surfacing suspicious behavior, but once available they provide clear evidence of malicious actions like credential misuse or data exfiltration.

GuardDuty's finding was called "Credentials for the EC2 instance role guard-duty-project-TheRole-2sg5SqiWmOpD were used from a remote AWS account", which means the service detected that temporary security credentials assigned to an EC2 instance were being misused outside the expected environment. Anomaly detection was used because GuardDuty continuously analyzes logs and network activity, comparing them against baselines of normal behavior, and flagged this credential exfiltration attempt as a high-severity unauthorized access event.

GuardDuty's detailed finding reported that credentials created exclusively for an EC2 instance using the role guard-duty-project-TheRole-2sg5SqiWmOpD were used from a remote AWS account. It flagged this as UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS with a High severity, meaning the temporary security credentials were stolen and then used outside the original environment to access resources like the S3 bucket. The finding included details such as the resource ID, access key ID, principal ID, and the specific time the suspicious activity occurred.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-security-guardduty_v1w2x3y4)

---

## Extra: Malware Protection

To test Malware Protection, I uploaded an EICAR test file to my S3 bucket. The uploaded file won't actually cause damage because the EICAR test file is a harmless string of code specifically designed to trigger antivirus and security tools as if it were malware. This makes it a safe way to verify that GuardDuty’s malware protection is working correctly without introducing any real threats into the environment.

Once I uploaded the file, GuardDuty instantly triggered a finding called Object:S3/MaliciousFile, reporting that the EICAR test file in my S3 bucket was detected as a potential security risk. This verified that GuardDuty’s Malware Protection feature is active and capable of scanning S3 objects for malicious content, flagging them as high severity threats even when they are harmless test files.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-security-guardduty_sm42x3y4)

---
