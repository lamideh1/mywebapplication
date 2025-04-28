# mywebapplication
Step 1: Create Your GitHub Repository
Create a GitHub repository:

Go to GitHub and sign in.

Click the New button to create a new repository.

Name your repository (e.g., jenkins-pipeline-demo) and click Create repository.

Follow the instructions to push your local code to GitHub (for now, just create a basic file like index.html or app.py).

Step 2: Install Jenkins on AWS (if not done already)
You mentioned your Jenkins is on AWS, so here’s a quick recap of setting up Jenkins on an AWS EC2 instance.

Install Jenkins on your EC2 instance:

SSH into your EC2 instance:

bash
Copy
Edit
ssh -i "your-key.pem" ubuntu@your-ec2-public-ip
Install Java (Jenkins requires Java to run):

bash
Copy
Edit
sudo apt update
sudo apt install openjdk-11-jdk
Install Jenkins:

bash
Copy
Edit
sudo wget -q -O - https://pkg.jenkins.io/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian/ stable main > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
Start Jenkins:

bash
Copy
Edit
sudo systemctl start jenkins
sudo systemctl enable jenkins
Access Jenkins by opening http://<your-ec2-public-ip>:8080 in your browser.

Install required plugins in Jenkins:

Go to Manage Jenkins > Manage Plugins.

Install Git plugin, Docker plugin, and any other plugins that you will need (like Pipeline plugin).

Step 3: Setup Jenkins Credentials for GitHub
Create GitHub credentials in Jenkins:

Go to Jenkins > Manage Jenkins > Manage Credentials.

Add a new credential for your GitHub account:

Kind: Username with password.

Username: Your GitHub username.

Password: Personal Access Token (generated from GitHub).

Step 4: Create Jenkins Pipeline Job
Create a new Jenkins Pipeline Job:

In Jenkins, click New Item.

Name the item (e.g., MyPipelineJob) and select Pipeline.

Click OK.

Configure SCM in Jenkins Pipeline:

In the Pipeline section of your job configuration, configure the following:

Definition: Pipeline script from SCM.

SCM: Git.

Repository URL: Your GitHub repository URL (e.g., https://github.com/your-username/jenkins-pipeline-demo.git).

Credentials: Select the credentials you created for GitHub.

Branch: main (or the branch you are working on).

Step 5: Create Jenkinsfile in Your GitHub Repository
Create the Jenkinsfile in the root of your repository (jenkins-pipeline-demo).

Here’s an example Jenkinsfile:

groovy
Copy
Edit
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-web-app'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                git url: 'https://github.com/your-username/jenkins-pipeline-demo.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run tests (replace with your own test commands)
                    sh 'echo "Running tests..."'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Run the Docker container
                    sh 'docker run -d -p 8080:80 ${DOCKER_IMAGE}'
                }
            }
        }
    }
}
Push the Jenkinsfile to GitHub:

Open Visual Studio Code (VSCode).

Open the repository folder.

Create a file named Jenkinsfile in the root of the repository and add the script above.

Stage, commit, and push the changes to GitHub:

bash
Copy
Edit
git add Jenkinsfile
git commit -m "Added Jenkinsfile"
git push origin main
Step 6: Test the Jenkins Pipeline
Run the Jenkins Job:

Go to your Jenkins pipeline job.

Click Build Now to manually trigger the build.

Jenkins will now pull the repository, build the Docker image, run tests (if any), and deploy the app (running it inside a container).

View the output:

You can view the logs of each stage in the Jenkins job to see how the process is going.

For Docker, Jenkins will run the container and expose it to http://<your-ec2-ip>:8080.

Step 7: Dockerize Your Web Application
Create a Dockerfile in your repository: If you haven’t already, create a Dockerfile in the root of the project that looks like this:

dockerfile
Copy
Edit
# Use the official Nginx base image
FROM nginx:alpine

# Copy index.html to the Nginx default public directory
COPY index.html /usr/share/nginx/html/index.html

# Expose the port the app will run on
EXPOSE 80
Test the Dockerfile locally:

Build the Docker image locally to make sure everything works.

bash
Copy
Edit
docker build -t my-web-app .
docker run -d -p 8080:80 my-web-app
Push changes to GitHub: After updating your Dockerfile, stage, commit, and push the changes:

bash
Copy
Edit
git add Dockerfile
git commit -m "Added Dockerfile"
git push origin main
Step 8: Automate Docker Image Push (Optional)
You can set up Jenkins to automatically push the built Docker image to Docker Hub after the successful build.

Configure Docker Hub credentials in Jenkins.

Add a new stage in your Jenkinsfile to push the image to Docker Hub:

groovy
Copy
Edit
stage('Push Docker Image') {
    steps {
        script {
            withDockerRegistry([credentialsId: 'dockerhub-credentials', url: 'https://index.docker.io/v1/']) {
                sh 'docker push my-web-app:latest'
            }
        }
    }
}
Final Steps: Automate Everything
Trigger Build on Code Changes:

Set up Webhooks in GitHub to automatically trigger Jenkins builds on code changes.

Go to your GitHub repository.

Under Settings > Webhooks, add a webhook pointing to your Jenkins server's webhook endpoint.

Monitor Builds:

Your Jenkins job will now automatically run the pipeline every time code is pushed to GitHub, creating Docker images, running tests, and deploying the app.

