# Secure Static Website on AWS

## Overview

This project demonstrates how to host a static website securely on Amazon Web Services (AWS) by leveraging several key services:

* **Amazon S3:** For secure and scalable storage of website assets (HTML, CSS, JavaScript, images). The bucket is configured for private access.
* **AWS Identity and Access Management (IAM):** To create a role with specific permissions, allowing the EC2 instance to securely access the S3 bucket without embedding access keys directly on the instance.
* **Amazon Elastic Compute Cloud (EC2):** To host the Apache web server that serves the website content.
* **Amazon CloudWatch:** For basic monitoring of the EC2 instance's performance and health.

This setup emphasizes security by ensuring website assets are not publicly accessible in S3 and by granting the web server only the necessary read-only permissions through an IAM role.

## Architecture
1.  Users access the website via HTTPS (configured on the EC2 instance - *Note: You might need to add details about HTTPS configuration if you implemented it*).
2.  The EC2 instance, running Apache, serves the main HTML files.
3.  When the website needs assets (images, CSS, JavaScript), the Apache server on the EC2 instance uses the associated IAM role to request and retrieve these files from the private S3 bucket.
4.  Amazon CloudWatch monitors the EC2 instance for performance and health metrics.

## Prerequisites

* An active AWS account.
* AWS CLI installed and configured with appropriate credentials to manage resources (used for initial S3 upload).
* Basic understanding of AWS services (S3, IAM, EC2, CloudWatch).
* Your static website files (HTML, CSS, JavaScript, images).
* An SSH client to connect to the EC2 instance.
* (Optional) A key pair for SSH access to the EC2 instance.

## Deployment Steps

1.  **Create a Private S3 Bucket:**
    * Created an S3 bucket in your desired AWS region.
    * Ensured that "Block all public access" settings were enabled for the bucket.

2.  **Upload Website Assets to S3:**
    * Used the AWS CLI `s3 sync` command to upload the website files to the created S3 bucket:
        ```bash
        aws s3 sync <local-website-directory> s3://<your-s3-bucket-name>/
        ```
        *(Replace `<local-website-directory>` with the path to your website files and `<your-s3-bucket-name>` with your S3 bucket name).*

3.  **Create an IAM Role for EC2:**
    * Created an IAM role with "AWS service" as the trusted entity and selected "EC2" as the use case.
    * Attached a custom IAM policy to this role granting read-only access to your specific S3 bucket. The policy included actions like `s3:GetObject` and `s3:ListBucket` on the bucket ARN and its contents.
    * Named the IAM role (e.g., `EC2S3ReadOnlyRole`).

4.  **Launch an EC2 Instance:**
    * Launched an EC2 instance using your preferred AMI (e.g., Amazon Linux 2, Ubuntu Server).
    * Selected an appropriate instance type.
    * During the instance configuration, associated the IAM role created in the previous step (`EC2S3ReadOnlyRole`) with the instance.
    * Configured a security group to allow inbound HTTP (port 80) traffic from the internet (and SSH - port 22 - for administration).

5.  **Install and Configure Apache Web Server:**
    * Connected to the EC2 instance via SSH.
    * Updated the package repositories.
    * Installed the Apache web server (`httpd` on Amazon Linux, `apache2` on Ubuntu).
    * Started and enabled the Apache service to run on boot.

6.  **Configure Apache to Serve Content from S3:**
    * Installed the AWS CLI on the EC2 instance.
    * Used the AWS CLI `s3 sync` command on the EC2 instance to copy the website assets from the S3 bucket to the Apache document root (e.g., `/var/www/html/`):
        ```bash
        sudo aws s3 sync s3://<your-s3-bucket-name>/ /var/www/html/ --delete
        ```
        *(Replace `<your-s3-bucket-name>` with your S3 bucket name).*
    * Adjusted file ownership and permissions for the Apache user to access the website files.

7.  **Implement Basic CloudWatch Monitoring:**
    * Navigated to the CloudWatch service in the AWS Management Console.
    * Viewed the default metrics for the EC2 instance.
    * (Optional) Created a basic alarm for a metric like CPU Utilization.

## Next Steps and Potential Enhancements

* **Enable HTTPS:** Configure SSL/TLS on the Apache web server to serve the website over HTTPS.
* **Infrastructure as Code (IaC):** Use CloudFormation or Terraform to define and manage the infrastructure.
* **Deployment Automation:** Implement a CI/CD pipeline to automatically deploy website updates.
* **Content Delivery Network (CDN):** Integrate Amazon CloudFront to improve performance and scalability.
* **Detailed Monitoring and Logging:** Set up more comprehensive monitoring with CloudWatch and centralize logs with CloudWatch Logs.

## Author

Nawaz
