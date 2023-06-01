Deployment Instructions - Local

These instructions will guide you through the deployment of the application locally.

Step 1: Clone the Git Repository
Clone the Git repository to your local machine using the following command:

	git clone [repository-url]
	
Step 2: Docker Installation
Ensure that Docker is installed on your machine by visiting the Docker website (https://docs.docker.com/desktop/) and following the installation instructions. Verify the installation by running the following command in your terminal:

	docker --version

Make sure the installed version is displayed.

Step 3: Create Dockerfile
Create a Dockerfile by utilizing the index.js and package.json files found in the cloned repository. The Dockerfile specifies the instructions for building the Docker image.

Step 4: Create Docker Compose File
Create a docker-compose.yml file to define the services and configurations for running the application. This file will orchestrate the creation of the Docker image and the container.

Step 5: Run the Application
Run the following command to start the application and create the Docker image and container:

	docker-compose up -d
	
This command will build and start the services defined in the docker-compose.yml file.

Step 6: Verify Deployment
Access http://localhost:3000/api in your web browser. The endpoint should display "Hello, World!". Additionally, access http://localhost:3000/api/health to verify that the application is running smoothly and displays "Application is super healthy!".

Congratulations! The application has been successfully deployed locally.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Deploying to Amazon EKS with Docker and Jenkins

Prerequisites:

An Amazon Web Services (AWS) account
Docker installed
kubectl installed (follow the instructions at: https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
awscli installed (follow the instructions at: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
Optional: eksctl can be used to create an EKS cluster easily (refer to: https://anywhere.eks.amazonaws.com/docs/getting-started/install/)
Jenkins instance set up with a Pipeline (For this deployment, it is not recommended to run Jenkins as a Docker container unless you have prior experience with it. Running Jenkins as a container can potentially pose challenges in installing kubectl in the container, which are necessary for the pipeline setup.)

Configuration:

Step 1: AWS Configuration

Login to AWS and create an ECR repository (refer to: https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-create.html)
Create an EKS cluster (refer to: https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html)
Store your AWS access key ID, Secret access key, and ECR URI credentials for later use.

Step 2: Jenkins Configuration

Access your Jenkins dashboard in a web browser.
Install the required Jenkins plugins:
CloudBees AWS Credentials Plugin
Kubernetes Plugin
Docker Pipeline Plugin
AWS ECR Plugin

Configuring Jenkins for Docker and kubectl:

In order for Jenkins to successfully run the pipeline, it requires access to Docker and kubectl that are installed on the server. By default, this connection may not be established, and the necessary paths need to be added manually to Jenkins.

Follow these steps to add the paths:

Navigate to the location where Jenkins is installed on your system.
Example: /opt/homebrew/Cellar/jenkins-lts/2.401.1/homebrew.mxcl.jenkins-lts.plist
Open the homebrew.mxcl.jenkins-lts.plist file.
Locate the sections where Jenkins and kubectl are installed.
Add the paths for both Docker and kubectl installations. This addition should cover all the required paths by default.
	   
           <key>EnvironmentVariables</key>
	   <dict>
    	   <key>PATH</key>
           <string>/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/homebrew/bin</string>
	   </dict>
	
Step 3: Configure AWS Credentials in Jenkins

Go to "Manage Jenkins" > "Manage Credentials".
Click on "Global credentials" domain.
Click on "Add Credentials" on the left-hand side.
Select the "AWS Credentials" option from the "Kind" dropdown.
Enter a meaningful ID (e.g., awsCred) for your credentials.
Provide your AWS Access Key ID and Secret Access Key.
Optionally, add a description for the credentials.
Click "Add" to save the credentials.


Step 4: Repository Configuration

Create a file named "deployment.yml" in the root level of your repository. Make sure to use the AWS ECR URI in this deployment file (refer to the deployment.yml file in git repo)
Add a Jenkinsfile to the root level of your repository. Reference your Amazon ECR repository URI and the credential ID (e.g., awsCred) in the Jenkinsfile (refer to the provided Jenkinsfile in the git repo).
Jenkins will handle the cloning, building, tagging, pushing, and deployment of the image to your EKS cluster using the deployment file you created earlier.
The "Using curl" stage in the Jenkinsfile will output the response of the API endpoint in the Jenkins logs itself. You can verify this by running the following command in your command prompt: '$ curl -i external-ip-of-k8s-cluster/3000/api/health'. It should return a 200-OK response code.		

Step 5: Viewing the Web Application

Access the web application in your browser by going to the external IP of the Kubernetes cluster, which can be found in the final URL and the Jenkins logs (curl -i external-IP-of-K8s-cluster/3000/api/health).

Congratulations! You have successfully deployed a web application to Amazon EKS using Docker and Jenkins
