# cloud-project-repo
Repository for cloud project
http://surbhi-1st-cloud-bucket.s3-website.eu-north-1.amazonaws.com

## Project Purpose
I built this project to automate the hosting and deployment of a static HTML website on AWS using Infrastructure as Code (IaC). To manage the project, I first created this GitHub repository with two separate branches: ‘main’ (as the default branch) and ‘dev’. Under the ‘main’ branch, I structured the repository into two directories (‘cloudformation/’ and ‘website/’) to store the source files. 

Instead of configuring AWS manually through the console, I wrote a CloudFormation YAML template, saved it in the repo, and used it to handle the provisioning. The setup adheres to modern cloud standards: public access is managed strictly through a bucket policy, and S3 Access Control Lists (ACLs) are completely disabled.

## AWS Services Used
* **Amazon S3**: Hosts the static website files. It is configured for static website hosting with ‘index.html’ as the root index document.
* **AWS CloudFormation**: Used as the IaC tool to spin up the S3 bucket and attach the required public read policy as a single stack.

## Deployment Steps

### Prerequisites
* Active AWS account with permissions for S3 and CloudFormation.
* Access to the AWS Management Console.

### Step 1: Repository and Infrastructure Configuration
The repository is structured with the following files saved on the ‘main’ branch:
* ‘cloudformation/template.yaml’ — The CloudFormation template configured to meet specific security and access criteria:
  * **ObjectOwnership**: Set to ‘BucketOwnerEnforced’ to completely turn off S3 ACLs.
  * **PublicAccessBlockConfiguration**: Set ‘BlockPublicPolicy’ to ‘false’ and ‘RestrictPublicBuckets’ to ‘false’. This explicitly permits the bucket policy to open public traffic to the hosted files while keeping other block elements secure.
  * **BucketPolicy**: Configured a statement granting explicit ‘s3:GetObject’ permissions to anyone (‘Principal: "*"‘).
* ‘website/index.html’ — The frontend static HTML file for the website.

### Step 2: Deploy the CloudFormation Stack
I deployed the stack using the name ‘surbhi-web-stack’. 
* **Using the AWS Console:**
  1. Go to the CloudFormation dashboard.
  2. Click **Create stack** -> **With new resources**.
  3. Upload the ‘template.yaml’ file from the ‘cloudformation/’ directory and name the stack ‘surbhi-web-stack’.

### Step 3: Upload the Website Files
After the stack status changed to ‘CREATE_COMPLETE’, I uploaded the code from the ‘website/’ directory into the new S3 bucket (‘Surbhi-1st-cloud-bucket’):
1. Open the Amazon S3 console and click on the newly created bucket (‘Surbhi-1st-cloud-bucket’). 
2. Click the **Upload** button. 
3. Add the ‘index.html’ file from the local ‘website/’ directory and complete the upload wizard.

### Step 4: Verification
To verify that the static website was deployed successfully and public access rules were applied correctly, I performed the following checks:
1. Opened the CloudFormation console, selected ‘surbhi-web-stack’, and checked the **Outputs** tab to retrieve the automatically generated S3 Website Endpoint URL.
2. Navigated to that public URL in a web browser to confirm that the ‘index.html’ file renders properly on the internet.
3. Verified the security criteria by checking the S3 bucket permissions dashboard to ensure that Access Control Lists (ACLs) show as completely disabled (‘Bucket owner enforced’) and that the content access is handled safely and exclusively by the bucket policy.


## Reflection Questions

### 1. Why is Amazon S3 suitable for static website hosting?
Amazon S3 is ideal for static websites because it completely removes the need to maintain or provision actual servers. Since static sites only consist of pre-built files like HTML, CSS, and images, S3 can serve them directly to users over the web. This makes hosting highly scalable, reliable, and incredibly low-maintenance.

### 2. What costs may occur?
While S3 is very cheap, costs can come from three main areas:
* Storage: The amount of data your index.html and other website assets take up in the bucket.
* Data Transfer: The bandwidth used when people visit your website and download the files to their browsers.
* Requests: The number of times users or deployment tools read, write, or list objects in the bucket (GET and PUT requests).

### 3. What happens when the CloudFormation stack is deleted?
When you delete the surbhi-web-stack, CloudFormation will attempt to tear down and remove all the resources it created, including the S3 bucket and its bucket policy. However, because S3 buckets must be entirely empty before they can be deleted, the stack deletion will fail or skip the bucket if the index.html file is still inside it. You have to manually empty the bucket first if you want CloudFormation to delete it successfully.
