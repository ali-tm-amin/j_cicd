# Setting up a Job in Jenkins

![](/images/cicd_jenkins.png)

![](/images/jenkins.png)

**Step 1**: Create new item
On the left you should see `New item`, select that.

**Step 2**: Create job
Under Enter an item name write the appropriate name for your job

## SSH Connection Between Github and Jenkins 

![](/images/cicd.png)

**Step 1**: Generate a new key

Generate a new ssh key in your localhost and name is `YOURNAMEjenkins`(eg alijenkins).

**Step 2**: Copy Key into Github

Go into your repo and select `Settings`

Select `Deploy Keys` and select `Add deploy key`

Copy the `public ssh key` into key and title `YOURNAMEjenkins`

Now select `Add key`

**Step 3**: Connect to Jenckins

Create a new Jenkins item and select `Freestyle Project` Set up the following configurations:

**Discard old builds**: 3

**Github project**: Insert GitHub HTTPS repo link 

**Restrict where this project can be run**: logical expression to specify which agent to execute builds of the project (e.g. sparta-ubuntu-node) 

**Git**: 
  * **Repository URL**: Insert GitHub SSH repo link 

  * **Credential**: Add -> Jenkins -> Key -> Insert private ssh key 

**Branches to build**: */main

**Github hook trigger for GITScm polling**: check this option 

## Setting Up a Webhook:
Setting up the webhook allows GitHub to trigger Jenkins to start a new build whenever a new commit is pushed.
- In the GitHub repository that is to be linked to Jenkins, create a new Webhook (Settings-->Webhooks-->Add webhook)

- **Payload URL**: Add the URL (usually specified with ip and port) with /github-webhook/ appended at the end ie http://18.130.227.98:8080/github-webhook/

**Content type**: Select application/json

- Any new pushes to the repository should now trigger a new build, shown in

- Build History where the Console Output can be read for each individual build

## Building an AMI
An Amazon Machine Image is a special type of virtual appliance that is used to create a virtual machine within the Amazon Elastic Compute Cloud. It serves as the basic unit of deployment for services delivered using EC2.

- Select the `EC2 instance` then click `Action` Choose `Image and Templates` then `Create Image` 
- Input ami image name, description then key(Name -> eng99_ali_db_ami) 
- Now select `Create` 

Your AMI should now be found on the AMI section on the dashboard

