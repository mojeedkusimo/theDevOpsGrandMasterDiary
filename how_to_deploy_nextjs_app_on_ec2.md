# HOW TO DEPLOY NEXTJS APP ON AWS EC2 INSTANCE

### I was initially looking for something simple and quick to setup a nextjs app on AWS using Elastic BeanStalk as I was responsible for deploying a team project [rentlify.com.ng](http://rentlify-proj-2071263833.af-south-1.elb.amazonaws.com/). Thanks to a couple of challenges along the way, partly due to not understanding some of the commands used, the search never seemed to end.

### It then occurred to me that perhaps I should try setting up my own server from scratch, so in this article I would be sharing some guide on how I setup a simple nextjs app on EC2 under the outline below:

#### 1. Launch a virtual server
#### 2. Install nodejs, nextjs and pm2
#### 3. Install nginx

### Trust me, it is actually not as difficult as it may seem.

## 1. Launch a virtual server, AWS EC2.

### Logging into the [AWS Management Console](https://aws.amazon.com/console/), I prefer to do all the setup in the nearest region to me i.e. Africa (Cape Town) af-south-1.
![region-selection](https://mojeedkusimo-public-asset.s3.af-south-1.amazonaws.com/region-selection.PNG) 

### Then search for EC2 at the top left seach bar ![ec2-search](https://mojeedkusimo-public-asset.s3.af-south-1.amazonaws.com/ec2-search.PNG)

### Then Click on the orange button labelled 'Launch Instance' ![launch-selection](https://mojeedkusimo-public-asset.s3.af-south-1.amazonaws.com/launch-selection.PNG)

### You can give your instance a name for easy identification and leave most of the default selections especially ones marked 'Free tier'

### For simplicit, the section 'Key pair (Login)' can have 'Proceed without key pair' since we are later going to connect to the instace via the browser.

### As for the 'Network settings', check both HTTP and HTTPS so the server can receive traffic over the internet. Then add another rule as can be seen below:

![security-grp-rule](https://mojeedkusimo-public-asset.s3.af-south-1.amazonaws.com/security-grp-rule.PNG) 

### This is because the default port for starting a nextjs app is 3000, a guide to change port will be shown later.

### Finally, launch your new instance.

## 2. Install nodejs, nextjs and pm2

### Now, our server is up so it is time to do some setups. 

### You would need to click on button in the screenshot in order 'Connect to instance':

![connect-to-instance](https://mojeedkusimo-public-asset.s3.af-south-1.amazonaws.com/connect-to-instance.PNG)

### Afterwards, you select the 'Connect using EC2 Instance Connect' to allow us access the server via the browser.

### Since the Amazon Linux 2 server uses Fedora distro which is a .rpm-based Linux Distribution, we would be using dnf (Dandified YUM) to install or manage packages. Firstly, lets update the repository with below command:

### sudo dnf update 

### The sudo is required as installation usually needs an elevated permission (or admin profile) to execute.

### Then execute the following commands:

### sudo dnf install nodejs -y
### npx create-next-app@latest
### sudo npm install -g pm2

### The first command installs nodejs with yes (y) flag telling the system to accept the installation immediately while the second is just to install the latest nextjs app which you follow the propmts in sequence.

### As for the last command, this is the installation of pm2 which is nodejs process manager. Ordinarily, the application would start if only npm is used. However, it gets killed as soon as the session is terminated i.e the browser is closed. Thus, if started with pm2, it keeps the application alive thereby running like a system process. Another thing to note is the g flag which indicates that pm2 should be installed globally. This gives it more control and make it available for use outside the current application.

### Now, lets change directory into the new application folder created (in my case app name was given as aws-nextjs-3) for nextjs and create production build with below commands:

### cd aws-nextjs-3
### npm run build

### The build is necessary because it is lighter and easier to run on the server with limited resources. If run in dev mode (npm run dev), it may require more resources which in most cases are outside 'Free tier'

### Running the application using pm2 is done by executing below command:

### pm2 start npm --name="my-test-app" -- run start

### where my-test-app is just a name which can be anything for easy identification as there can be other applications managed by the pm2 running on the same server

## 3. Install nginx

### At this point, we can actually see the application running by simply sopying the public IP - : - port 3000. In my case, that is:

### 13.246.240.31:3000

### However, this does not look too good for the port to be displayed after the IP. It is much more decent to have just the IP which in most cases is usually maaped to a domain name.

### This is where nginx comes in. One of the uses of nginx is to act as a reverse proxy such that when a request hits the IP alone, the server is able handle it and route the request to the application running on the server. Installing it is done via below command:

### sudo dnf insatll nginx

### Now, we need to tell the server how we want it to handle traffic by creating a config file ending with .conf e.g server.comf and put at below directory

### /etc/nginx/conf.d/

### The content should look like ![this](https://mojeedkusimo-public-asset.s3.af-south-1.amazonaws.com/nginx-conf.PNG)

### Then start the nginx using this

### sudo sytemctl start nginx

### And viola! The application is accessible directly on the IP

 