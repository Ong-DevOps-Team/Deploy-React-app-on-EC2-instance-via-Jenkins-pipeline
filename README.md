# Create a CI/CD flow to deploy the React App via Jenkins

#### Description:

- Launch 2 EC2 server, 1st for Jenkins and 2nd for App deployment.
- On 1st server install and configure Jenkins
- On 2nd server, install the dependencies to run the react app.
- Create a react project.
- Upload this react project in GitHub.
- Make a CI/CD flow to deploy the code via Jenkins.

#### Step-1: Launch 2 EC2 server, 1st for Jenkins and 2nd for App deployment

Firstly, log in to the AWS management console.

![Img-1](https://user-images.githubusercontent.com/74168188/178555843-f062573f-166c-4b06-b947-d2d11da46507.png)

After login, navigate to the search bar, type EC2, and select EC2

![Img-2](https://user-images.githubusercontent.com/74168188/178555883-5e169bfd-d205-4b99-9992-8dca7e957ff2.png)

Now, the EC2 dashboard will appear. Click Instances

![Img-3](https://user-images.githubusercontent.com/74168188/178555921-6213742a-726c-4532-923e-3edc4c4d1413.png)

Click the Launch instances button.

![Img-4](https://user-images.githubusercontent.com/74168188/178555939-529e74f0-6ece-43dc-aa1c-98d6b7f2deda.png)

To launch an EC2 instance, a few details are required, i.e., instance name, OS image (AMI), instance type, etc.

![5](https://user-images.githubusercontent.com/74168188/181441501-ce1a5d28-3114-4756-81a6-d45c6f8f4ebf.png)
![Img-6](https://user-images.githubusercontent.com/74168188/178556050-f90b180a-0dca-48fb-b30b-8365f9ac8f28.png)
![Img-7](https://user-images.githubusercontent.com/74168188/178556073-b0a18233-2e38-4dbd-926d-f7e411f7fa06.png)

Select a key pair to attach with the instance to log in with that key. If you do not have key pair, then create a new key pair by clicking Create a new key pair. Moreover, according to usage, download key pair in .pem or .ppk format.

![8](https://user-images.githubusercontent.com/74168188/179713553-227871ba-db62-496f-b361-2cf1ef3ff50e.png)

Now, to allow traffic from the internet, we need to create a rule in network settings.

![9](https://user-images.githubusercontent.com/74168188/179716719-a040568f-8b89-43df-817c-f39b35b19337.png)

We are good to go to launch an instance for this. Click the Launch instance button. If everything is correctly set up, you will find a success message on the screen. Click on the instance id to navigate to the EC2 dashboard.

![Img-11](https://user-images.githubusercontent.com/74168188/178556301-ac2e5bdd-7efa-4ad4-99d9-b669e599eefd.png)

Similarly, create another EC2 instance for app development.

#### Step-2: On 1st server install and configure Jenkins

Now, take the public IP from the EC2 dashboard and use it to login inside the instance using ssh. First move to the directory where the key is downloaded. In my case it's in downloads directory.

![12](https://user-images.githubusercontent.com/74168188/181449836-51c09040-e9b6-4d04-be5e-ac79e30fd525.png)

After logging in, first update local package index using ```sudo apt update```

![13](https://user-images.githubusercontent.com/74168188/181450073-47a9c873-c066-4a40-a426-ca5d1afac2ac.png)
![14](https://user-images.githubusercontent.com/74168188/181450200-a5e2c56b-3e89-4a04-b10d-648c5ead66c9.png)

Jenkins require jdk to run so let's install jdk using ```sudo apt install default-jdk -y```

![15](https://user-images.githubusercontent.com/74168188/181450766-ac29f9de-47c5-4bb9-a82b-bdb490df693a.png)
![16](https://user-images.githubusercontent.com/74168188/181450818-852df5e0-b158-470a-afc2-b85297d079ac.png)

To check jdk is successfully installed or not use ```java -version```

![17](https://user-images.githubusercontent.com/74168188/181451166-72af8b22-9c0d-4cd9-8df5-b6c4aa791ae9.png)

Now to install jenkins, follow below steps these are provided on jenkins official website also:

1. Add the key to your system using:
```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
    /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

2. Then add a Jenkins apt repository entry:
```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
```

3. Update your local package index, then finally install Jenkins:
```
sudo apt-get update
  sudo apt-get install fontconfig openjdk-11-jre
  sudo apt-get install jenkins
```

![18](https://user-images.githubusercontent.com/74168188/181455964-6dd2eedc-e85d-45ad-9fce-c184784e6c6e.png)
![19](https://user-images.githubusercontent.com/74168188/181455980-8dc1ef3f-a361-4241-bae3-ff7020c35ca3.png)

To check jenkins is successfully installed or not use ```jenkins --version```

![20](https://user-images.githubusercontent.com/74168188/181456518-9104c47e-7390-4705-941b-7e8e4adf7b08.png)

Now, check the status of jenkins service using ```sudo systemctl status jenkins```

![21](https://user-images.githubusercontent.com/74168188/181457434-c493a5e4-5b07-448e-bdea-3b9ca41f8707.png)

As jenkins, by default runs on port number 8080 so we need to create firewall rule to enable port 8080. So that jenkins can be accessed using **INSTANCE_PUBLIC_IP:8080**

For this click **Security** as show in screenhot below then on secuity group that start from **sg-......**

![22](https://user-images.githubusercontent.com/74168188/181459864-1875260a-7227-467a-bfba-3e11c54b2aa4.png)

Here, click **Edit inbound rules** then on **Add rule**

![23](https://user-images.githubusercontent.com/74168188/181460272-a2799141-ab15-4855-9e81-588e3f20ac2b.png)

add rule for 8080 port from anywhere i.e. 0.0.0.0/0 then click **Save rules**

![24](https://user-images.githubusercontent.com/74168188/181460292-a77b09c4-888a-40b9-9663-826859ca86d3.png)


Now try to hit ```<INSTANCE_PUBLIC_IP>:8080```

![25](https://user-images.githubusercontent.com/74168188/181475134-e84bd05b-f0cb-49fc-b725-96e9334738cb.png)

As stated in above screenshot, to login to jenkins dashboard we have to provide admin password which is stored in **/var/lib/jenkins/secrets/initialAdminPassword** to see password use ```sudo cat /var/lib/jenkins/secrets/initialAdminPassword```

![26](https://user-images.githubusercontent.com/74168188/181476533-8bf12533-29bf-447c-ac96-0227f436fbf1.png)

Now, its asking to install plugins but we don't want to install so click on cancel

![27](https://user-images.githubusercontent.com/74168188/181477176-a7723ced-6383-4c0f-ae81-4cd60cce57e1.png)
![28](https://user-images.githubusercontent.com/74168188/181477283-5a102655-29f7-4f26-aad6-c3b55bcba231.png)

then click **start using jenkins**

![29](https://user-images.githubusercontent.com/74168188/181477671-18ea4985-cbfa-41fa-9bdf-5c0070e9128a.png)

As we have cancelled to install jenkins plugins so it also cancelled setting admin password. So we need to set admin password else if we logout we won't be able to login again.

So let's create/change admin password. For this click **Manage Jenkins** then in Security section click **Manage Users**

![30](https://user-images.githubusercontent.com/74168188/181479379-65711bc8-3d42-49fe-8d51-f9c409cb9a6f.png)

Now click on setting button of admin user

![31](https://user-images.githubusercontent.com/74168188/181479581-63f91d1b-5625-417c-8131-0aa1f3b3e1ef.png)

Scroll down and look for password section then enter password that you want and click **Save**

![32](https://user-images.githubusercontent.com/74168188/181479969-11213b98-76a4-4076-9e61-9c0eb23a5cc0.png)

Now again search for **INSTANCE_PUBLIC_IP:8080** form any browser and enter username and password

![33](https://user-images.githubusercontent.com/74168188/181480672-b72c5430-1905-400c-b796-4d13a79af5af.png)
![34](https://user-images.githubusercontent.com/74168188/181480741-3e7082bd-dd08-4bfe-be74-8ff12bc2d96f.png)

Now let's quickly configure 2nd instance as jenkins worker then we have to build react app and deploy it on nginx.

Again we have to install jdk in worker node also. For this, first update local package index using ```sudo apt update``` then install jdk using ```sudo apt install default-jdk -y```

![35](https://user-images.githubusercontent.com/74168188/181504654-1a71e746-5316-4a6e-9074-2ac9ab6dac29.png)
![36](https://user-images.githubusercontent.com/74168188/181504722-ae88e8ef-7ffd-4e3f-a67f-657f43b48c0c.png)
![37](https://user-images.githubusercontent.com/74168188/181505121-5d5a0a53-77ba-4ad4-aae2-ec75fec57297.png)
![38](https://user-images.githubusercontent.com/74168188/181505133-53a6230f-9bf8-4799-af59-cb6de7835dc9.png)

As this instance is linux type so to work as worker node we need to launch agents over SSH. For this we need to install **SSH Build Agents** plugin. For this click manage jenkins

![39](https://user-images.githubusercontent.com/74168188/181510664-45a5885c-2140-4fa3-b395-5dd000d3e814.png)

In this page click **Manage Plugins**

![40](https://user-images.githubusercontent.com/74168188/181511041-31a701c0-2f2f-4542-a59d-98e6e28eed1c.png)

In available search **SSH Build Agents** and select then click **Download now and install after restart**

![41](https://user-images.githubusercontent.com/74168188/181512277-bb55f41f-c723-405d-970c-44f066d2a1e3.png)
![42](https://user-images.githubusercontent.com/74168188/181512423-5d3c1123-cdb2-4ec4-ae7a-ad08c0e3dc77.png)

Now we are good to go to connect worker node with master node.

Now, head to the jenkins dashboard. Click **Manage Jenkins** then click **Manage Nodes and Clouds**

![43](https://user-images.githubusercontent.com/74168188/181515177-eb22f77f-d36d-4eee-80ba-4bc0f0cd84ec.png)
![44](https://user-images.githubusercontent.com/74168188/181515250-b0536d45-d4cb-4bcc-a3bb-59281fc6e019.png)

Click **New Node**

![45](https://user-images.githubusercontent.com/74168188/181515327-bd1e2dd0-aff7-4772-9a04-858e38af8c0b.png)

Now, enter node name and select **Permanent Agent** then click **create**

![46](https://user-images.githubusercontent.com/74168188/181515396-59e2d518-c0f3-4b48-8476-b6b03d807689.png)

Fill few details like **No. of executors**, **Remote root directory**, **Usage**, **Launch method**, **Host**, **Credentials**, **Host Key Verification Strategy** are required.

![47](https://user-images.githubusercontent.com/74168188/181517007-088dfa50-f1c1-404a-b577-7c680bedfdb8.png)
![48](https://user-images.githubusercontent.com/74168188/181517053-c496f674-3ebf-4750-8064-f6be09d45481.png)

To connect this worker node to master node jenkins need to install agent for this jenkins requires username and private key of worker node.

![49](https://user-images.githubusercontent.com/74168188/181517248-a5d96fe3-d1ef-4615-a682-a194c09b75d3.png)
![50](https://user-images.githubusercontent.com/74168188/181517267-86aa9552-d7c9-4060-8542-7301db4e2aa7.png)
![51](https://user-images.githubusercontent.com/74168188/181517286-30eb4e93-215e-428c-b49a-e0d313270944.png)

Then finally click save. Now navigate to **Manage Jenkins** then **Manage Nodes and Clouds**.

![52](https://user-images.githubusercontent.com/74168188/181517431-19e18f63-8dfc-495d-8e77-138a49000e1a.png)

As you can see worker node is successfully connected to master node.

#### Step-3: On 2nd server, install the dependencies to run the react app

First, we need to install nodejs. npm comes with nodejs bundled. To install nodejs, use below commands:
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

![53](https://user-images.githubusercontent.com/74168188/179720815-92a33f24-83a2-4aed-aa0c-a5fa0b7c28bf.png)
![54](https://user-images.githubusercontent.com/74168188/179720992-b54165f1-65d8-4a96-838c-1ddaa744bc12.png)
![55](https://user-images.githubusercontent.com/74168188/179721130-662d3cd1-a868-4779-a976-db75d49f2c63.png)
![56](https://user-images.githubusercontent.com/74168188/179741220-996e1ba3-f774-46e2-84ba-790513ae1ccb.png)

To check nodejs is successfully installed or not use ```node -v```

![57](https://user-images.githubusercontent.com/74168188/179741532-8f2e1597-a542-4693-ae08-facd3d630b2b.png)

Also, npm and npx is already installed. Use ```npm -v``` and ```npx -v``` to check version of npm and npx

![58](https://user-images.githubusercontent.com/74168188/179742147-b31454f7-ddcb-42c0-8cb6-7197600e82a8.png)

#### Step-4: Create a react project

Now to create react project we use yarn instead of npx. For creating react project use below commands:

```
sudo npm install -g yarn
sudo yarn global add create-react-app
sudo create-react-app <APP_NAME>
```

![59](https://user-images.githubusercontent.com/74168188/181865007-43e61ebb-e7ca-43a0-9084-9cd8fd74c287.png)
![60](https://user-images.githubusercontent.com/74168188/181865021-d4321b24-0e19-46cc-b73b-efc733c425be.png)

#### Step-5: Upload this react project in GitHub

React project is successfully created now time to upload this project to GitHub. For this create an empty repo in GitHub.

![61](https://user-images.githubusercontent.com/74168188/181865211-0d1a3f4e-e5cb-4b1b-9b6f-3f2608b55883.png)

Now copy the repo url and clone in from worker node using ```git clone https://github.com/mannansiddiqui/react-app-using-jenkins-pipeline.git```

![62](https://user-images.githubusercontent.com/74168188/181865303-a8faa082-0bad-4ecb-bf57-f054002bb041.png)

Now move/copy all files/folders from react project directory i.e. **testapp** to repo directory i.e. **react-app-using-jenkins-pipeline** using ```cp -r testapp/* react-app-using-jenkins-pipeline/```

![63](https://user-images.githubusercontent.com/74168188/181865429-37e1769e-5fd2-4874-96fb-052211351293.png)

Now from repo directory i.e. **react-app-using-jenkins-pipeline** run ```git status```.

![64](https://user-images.githubusercontent.com/74168188/181865485-5fa4fbfd-42b1-4680-888a-fbf36fc3fa9c.png)

It will show all files untracked. To keep all files tracked by git use ```git add .```

![65](https://user-images.githubusercontent.com/74168188/181865545-e34b9cd6-698d-4b96-b374-828e74443ccf.png)

Now again run ```git status``` to check status

![66](https://user-images.githubusercontent.com/74168188/181865738-00431d2e-f484-4e4c-854b-3ecdc7932996.png)

As you can see now all files/folders are tracked by git. Now we have to commit all files/folders then push it to GitHub after providing GitHub credentials.

For commit use ```git commit -m "First Commit" .```

![67](https://user-images.githubusercontent.com/74168188/181865842-789848e9-5640-41fb-b96e-b649304f879c.png)
![68](https://user-images.githubusercontent.com/74168188/181865919-f1b95dd8-31b5-469e-aa10-ea4baaf0af54.png)

Now time to push to GitHub using ```git push```

![69](https://user-images.githubusercontent.com/74168188/181866068-60c4f58c-c990-4655-bb34-df0fc2f35462.png)
![70](https://user-images.githubusercontent.com/74168188/181866089-a72148dc-79a3-4f92-b5f4-fcdbd937b11d.png)

Files/Folders are successfully pushed to GitHub repo.

#### Step-6: Make a CI/CD flow to deploy the code via Jenkins

Now we need to create a pipeline for next steps. But before this we have to install some plugins. First we require a **Pipeline** plugin to create a pipeline. Second we require **GitHub** plugin so that jenkins can interact with GitHub to download repo and many more things.

To install plugins navigate to **Manage Jenkins** then **Manage Plugins** and in **Available** search for **Pipeline** plugin and **GitHub** plugin then select and click on **Download now and install after restart** also don't forget to click on **restart after download completes**.

![71](https://user-images.githubusercontent.com/74168188/181734347-2adb6040-bba8-453e-a59a-5276f40b4d23.png)
![72](https://user-images.githubusercontent.com/74168188/181734362-9f7caed2-8502-4542-9419-4dd460378ab7.png)

![73](https://user-images.githubusercontent.com/74168188/181734395-d9c499fe-258d-44b9-85ac-c70f92bee33e.png)
![74](https://user-images.githubusercontent.com/74168188/181734406-ac191322-5504-499e-a644-7c76b2886c1c.png)

After restart you will get jenkins dashboard

![75](https://user-images.githubusercontent.com/74168188/181736072-d4e7c06b-5e3f-4dac-90e4-f1d6974d94f4.png)

Time to create a job. Enter job name and select **Pipeline** then click on **OK**.

![76](https://user-images.githubusercontent.com/74168188/181736328-3cb1522d-339d-44ed-8f98-948427ec0f59.png)

We want as developer commit and push code to GitHub this job trigger and deploy the new code. For this we have to enable webhook inside GitHub repo.

First click on **settings** of GitHub repo

![77](https://user-images.githubusercontent.com/74168188/181866577-5d1ac8fa-dc70-4a4a-b02c-78589529f913.png)

Here click on **Webhooks**

![78](https://user-images.githubusercontent.com/74168188/181866610-4b9310d5-d9f4-43f5-9845-e21630bea186.png)

Now click on **Add Webhook** it will ask you GitHub password

![79](https://user-images.githubusercontent.com/74168188/181866650-7025b8ba-525a-423f-b9eb-34921b1bf49d.png)

Now provide details of jenkins master i.e. **Payload URL** ```http://<INSTANCE_PUBLIC_IP>:8080/github-webhook/``` and select Content type **application/json** lastly select **Just the push event** it means as the push event occurs in this repo webhook will trigger the jenkins job so click **Add webhook**.

![80](https://user-images.githubusercontent.com/74168188/181866928-f9aee701-2b5c-49ff-b2ae-f2dc0740530d.png)

Moving back to the job

![81](https://user-images.githubusercontent.com/74168188/181866460-b37a69dd-caea-4658-8849-ea96984e1e02.png)

In **Build Triggers** select **GitHub hook trigger for GITScm polling** this means that trigger this job as the event occurs on repo.

![82](https://user-images.githubusercontent.com/74168188/181867192-b90a866d-8dcf-4f82-9040-f967420c57ad.png)

Now write the Groovy script to deploy react app and click on **Save**

```
pipeline {
    agent { label 'Slave-1' }

    stages {
        stage('Deploy React App') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mannansiddiqui/react-app-using-jenkins-pipeline']]])
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/mannansiddiqui/react-app-using-jenkins-pipeline.git'
                sh 'sudo npm run build'
                sh 'sudo apt install nginx -y'
                sh 'sudo cp -r $WORKSPACE/build/* /var/www/html/'
                sh 'sudo systemctl restart nginx'
            }
        }
    }
}
```

![83](https://user-images.githubusercontent.com/74168188/182086230-51277e7e-5332-4c23-baef-ab7aa6f02621.png)

Now make changes in **App.js** file under **src** directory and commit so that job can be triggered automatically.

![84](https://user-images.githubusercontent.com/74168188/182087216-ba87c7e3-6dd7-4106-ac32-49c97c7ef8f3.png)
![85](https://user-images.githubusercontent.com/74168188/182087379-b48d5083-58b7-4a1b-a857-355f782ba7a5.png)

![86](https://user-images.githubusercontent.com/74168188/182087643-756ac225-2339-4170-8988-ff6ebde9a412.png)
![87](https://user-images.githubusercontent.com/74168188/182088235-c6333345-3034-405a-8e29-65e069c18793.png)

As you can see job is triggered as the push/commit event occurs and successfully completed.

Now let's hit ```<INSTANCE_PUBLIC_IP>``` and see results.

![88](https://user-images.githubusercontent.com/74168188/182088572-739ecafd-7dcf-4cdf-b863-95c81c80d370.png)

React app is not showing because we have'nt created rule for port no. 80 in security group. So, lets enable port 80.

In security group, click **Edit inbound rules** and allow port 80 and click **Save rules**

![89](https://user-images.githubusercontent.com/74168188/182089125-e9bdaf00-3e37-4939-8659-e3667676b653.png)

Now again hit ```<INSTANCE_PUBLIC_IP>```

![90](https://user-images.githubusercontent.com/74168188/182089300-9783d8e7-b761-4f34-b817-ee3f6c414ce0.png)

Site is showing now...

### Thank you...
