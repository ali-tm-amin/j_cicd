# Setting up a Job in Jenkins


![](/images/cicd_jenkins.png)


![](/images/Jenkins_agent.png)


# How to create a Jenkins CI/CD pipeline

## Install Java on to ec2 insta server

Ubuntu 18.04 includes Open JDK 11, which is an open-source variant of the JRE and JDK.
To install this version, first update the package index:
- sudo apt update
check if Java is already installed:
`java --version`
- If Java is not currently installed
`sudo apt install default-jre`
- To verify `java --version`
- install jdk
`sudo apt install default-jdk`
- To verify
`javac -version`
## Install Jenkins on Ubuntu 18.04
  ### Step 1 - Installing Jenkins
  - First, add the repository key to the system: 
  `wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -`
  - When the key is added, the system will return OK. Next, append the Debian package repository address to the server’s sources.list:
  `sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'`
  - `sudo apt update`
  - `sudo apt install jenkins`

	- For Long Term Support release:

`      curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
        /usr/share/keyrings/jenkins-keyring.asc > /dev/null
      echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
        https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
        /etc/apt/sources.list.d/jenkins.list > /dev/null
      sudo apt-get update
      sudo apt-get install jenkins`

  - For Installation of Java:
      `sudo apt update
      sudo apt search openjdk
      sudo apt install openjdk-11-jdk
      sudo apt install openjdk-11-jdk
      java -version
`
  ### Step 2 - Starting Jenkins
  - `sudo systemctl daemon-reload`
  - `sudo systemctl start jenkins`
  - `sudo systemctl status jenkins`
  ### Step 3 - Opening the Firewall
  - sudo ufw allow 8080
  - sudo ufw status
  - if firewall inactive run these commands:
  `sudo ufw allow OpenSSH` `sudo ufw enable`
  
  ### Step 4 - Setting Up Jenkins
 - visit Jenkins on its default port, 8080, using your server domain name or IP address: `http://your_server_ip_or_domain:8080`
 ![](/images/unlock-jenkins.png)

 - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
 

 - Copy the 32-character alphanumeric password from the terminal and paste it into the **Administrator password** field, then click **Continue**.
 ![](/images/customize_jenkins_screen_two.png)
![](/images/jenkins_create_user.png)
- On the left-hand side, click **Manage Jenkins**, and then click **Manage Plugins**.

- Click on the **Available** tab, and then enter **node** plugin at the top right.

- elect the checkbox next to **node.js** plugin, and then click **Install without restart**.




https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-18-04



## Step 2: Generate a new key

Generate a new ssh key in your localhost and name is `YOURNAMEjenkins`(eg alijenkins).

## Step 3: Copy Key into Github

Go into your repo and select `Settings`

Select `Deploy Keys` and select `Add deploy key`

Copy the `public ssh key` into key and title `YOURNAMEjenkins`

Now select `Add key`

## Step 4: Connect to Jenckins

Create a new Jenkins item and select `Freestyle Project` Set up the following configurations:

**Discard old builds**: 3

**Github project**: Insert GitHub HTTPS repo link 

**Restrict where this project can be run**: logical expression to specify which agent to execute builds of the project (e.g. sparta-ubuntu-node) 

**Git**: 
  * **Repository URL**: Insert GitHub SSH repo link 

  * **Credential**: Add -> Jenkins -> Key -> Insert private ssh key 

**Branches to build**: */main

**Github hook trigger for GITScm polling**: check this option 

## Step 5: Setting Up a Webhook:
Setting up the webhook allows GitHub to trigger Jenkins to start a new build whenever a new commit is pushed.
- In the GitHub repository that is to be linked to Jenkins, create a new Webhook (Settings-->Webhooks-->Add webhook)

- **Payload URL**: Add the URL (usually specified with ip and port) with /github-webhook/ appended at the end ie http://18.130.227.98:8080/github-webhook/

**Content type**: Select application/json

- Any new pushes to the repository should now trigger a new build, shown in

- Build History where the Console Output can be read for each individual build

## Step 6: Creating Jenkins Jobs
1. On the Jenkins Dashboard, click on `New Item`
2. Enter a the name in convetion for the job
3. Select `Freestyle project`
4. Click `Ok`
5. Create a job for CI, merging and deployment
![](/images/job1_test.png)

## Step 7: Continuous Integration (CI) Job

**General**
1. Click `Discard old builds` and keep the max number of build to 2
2. Click `GitHub project` and add the HTTP URL of the repository
**Office 365 Connector**

Click `Restrict where this project can be run`, then set it as `sparta-ubuntu-node`

**Source Code Management**
1. Select Git
2. In Repositories:
  - **Repository URL**: insert the SSH URL from GitHub
  - **Credentials**:
    - Next to `Credentials`, click `Add` > `Jenkins`
    - Select `Kind` as `SSH Username with private key`
    - Set a suitable description and enter the private key directly. The private key is in your ~/.ssh directory. Ensure that the begin and end text of the key is included.
    - With the credential added, select the one you created
  - **Branches to build**: set to */dev (dev branch)

**Build Triggers**
- Click *GitHub hook trigger for GITScm polling*

**Build Environment**
- Click *Provide Node & npm bin/ folder to PATH*

**Build**
1. Click *Add build step > Execute Shell*
2. In command, enter the following code:

`cd app
npm install
npm test`

**Post-build Actions**
1. *Select Add post-build action > Build other projects*
2. Insert the project name for the merge job
3. Ensure Trigger only if build is stable is selected

## Step 8: Merge Job
General
1. Click *Discard old builds* and keep the max number of build to 2
2. Click *GitHub project* and add the HTTP URL of the repository

**Office 365 Connector**
Click *Restrict where this project can be run*, then set it as `sparta-ubuntu-node`

**Source Code Management**
1. Select **Git**
2. In **Repositories**:
  - **Repository URL**: insert the SSH URL
  - **Credentials**: select the credential you created earlier
  **Branches to build**: set to */dev (dev branch)

**Build Environment**
Select **Provide Node & npm bin/ folder to PATH**

**Post-build Actions**
1. First, **select Add post-build action > Git Publisher**
2. Click **Push Only If Build Succeeds**
3. In **Branches**:
  - **Branch to push**: main
  - **Target remote name**: origin
4. Next, select **Add post-build action > Build other projects**
5. Insert the project name for the deploy job
6. Ensure **Trigger only if build is stable** is selected
E7. nsure the **Build other projects** block is below the **Git Publisher** block
![](/images/merge.png)
![](/images/job2_merge.png)


## Step 9: Provissioning database
General
1. Click *Discard old builds* and keep the max number of build to 2
2. Click *GitHub project* and add the HTTP URL of the repository

**Office 365 Connector**
Click *Restrict where this project can be run*, then set it as `sparta-ubuntu-node`

**Source Code Management**
1. Select **Git**
2. In **Repositories**:
  - **Repository URL**: insert the SSH URL
  - **Credentials**: select the credential you created earlier
  **Branches to build**: set to */dev (dev branch)

**Build Environment**
Select **Provide Node & npm bin/ folder to PATH**

**Post-build Actions**
1. First, **select Add post-build action > Git Publisher**
2. Click **Push Only If Build Succeeds**
3. In **Branches**:
  - **Branch to push**: main
  - **Target remote name**: origin
4. Next, select **Add post-build action > Build other projects**
5. Insert the project name for the deploy job
6. Ensure **Trigger only if build is stable** is selected
E7. nsure the **Build other projects** block is below the **Git Publisher** block
![](/images/job4.png)
## Step 10: EC2 Instance for Deployment
We will deploy our application on an EC2 instance.

1. Create a new EC2 instance
2. Choose `Ubuntu Server 16.04 LTS (HVM), SSD Volume Type` as the AMI
3. Choose `t2.micro` as the instance type (the default)
4. On Configure Instance Details:
  - Change the VPC to your VPC
  - Change subnet to your public subnet
  - Enable `Auto-assign Public IP`
5. Add a tag with the `Key` as `Name` and enter an appropriate name as the value
6. Enter a suitable name and description for the Security Group with the following rules:
  - SSH (22) with source `My IP` - allows you to SSH
  - SSH (22) with source `jenkins_server_ip/32` - allows Jenkins server to SSH
  - HTTP (80) with source `Anywhere` - allow access to the app
  - Custom TCP (3000) with source `0.0.0.0/0` - allow access to port 3000
7. Review and Launch
8. Select the existing DevOpsStudent key:pair option for SSH
9. Ensure the public NACL allows SSH (22) with source `jenkins_server_ip/32`
10. If the Jenkins server updates/reboots, the GitHub webhook, security group and NACL need to be modified

## Step 11: Continuous Deployment Job
**General**
1. Click `Discard old builds` and keep the max number of build to 2
2. Click `GitHub project` and add the HTTP URL of the repository

**Office 365 Connector**
- Click Restrict where this project can be run, then set it as `sparta-ubuntu-node`

**Source Code Management**
- Keep it at **None**

**Build Environment**
1. Select *Provide Node & npm bin/ folder to PATH*
2. Select *SSH Agent*:
  - Select *Specific credentials*
  - Select the SSH key for the EC2 instance (DevOpsStudent in this case)

**Build**
1. Select **Add build step > Execute Shell**

2. In command, insert the following code:

`rm -rf eng99_cicd_jenkins*
git clone -b main https://github.com/ali-tm-amin/j_cicd.git

rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@deploy_public_ip:/home/ubuntu/app
rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@deploy_public_ip:/home/ubuntu/app

ssh -A -o "StrictHostKeyChecking=no" ubuntu@deploy_public_ip <<EOF

    # 'kill' all running instances of node.js
    killall npm

    # run provisions file for dependencies
    cd /home/ubuntu/app/environment/app
    chmod +x provision.sh
    ./provision.sh

    # Install npm for remaining dependencies
    cd /home/ubuntu/app/app
    sudo npm install
    node seeds/seed.js

    # Run the app
    node app.js &
    
    EOF`

3. NOTE: the `deploy_public_ip` will need to be changed each time you re-run the deployment EC2 instance

## Step 12: Trigger the Builds!
1. Switch to the `dev` branch
2. Make any change to your repository
3. `Add`, `commit` and `push` your changes to the `dev` branch
4. A CI build will trigger
5. A merge build will only trigger if the tests in the CI build pass
6. After the merge build, the `dev` branch will merge with your `main` branch on GitHub
7. If the deployment build succeeds, the app will be running on its public IP!
8. NOTE: the image will only display if you access port 3000


## Bonus Step: Running in Vagrant
1. Run both the app and database using `vagrant up app` and `vagrant up db` respectively on separate terminals
2. SSH into the app using `vagrant ssh app`
3. Navigate to the `/home/ubuntu/` directory (you are placed in `/home/vagrant/` by default)
4. Go to the `/home/ubuntu/app/app` directory and run node seeds/seed.js to populate the database
5. Run `npm install` for any remaining dependencies
6. Execute `node app.js` to run the app
7. On a web browser, enter `development.local:3000` to show that the app is working
8. The posts page will open with `development.local:3000/posts`


