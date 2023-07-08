Automation using Kubernetes from Developer to Production System for CI/CD🤠
This is the upgraded version of my previous task, where used docker and Jenkins for Automation of the complete Job chain between Developer, Quality Assurance Team, and Production Team. In this task. We are going to use Kubernetes resources like Pods, ReplicaSet, Deployment, PVC, and Service in the place of Docker to perform CI/CD.⚡
Rohit Ghumare
Rohit Ghumare

·
Follow

7 min read
·
Jul 18, 2020

Listen


Share

Github+Jenkins+Docker Adv Task
This article is a simple integration of GitHub, Jenkins and Docker to automate the process of deploying a website by…
www.linkedin.com

https://www.inovex.de/blog/wp-content/uploads/2020/05/kubernetes-testing.gif
Kubernetes Testing
Kubernetes:-
Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications. Kubernetes provides you with:

Service discovery and load balancing
Kubernetes can expose a container using the DNS name or using its own IP address.
Storage orchestration
Kubernetes allows you to automatically mount a storage system of your choice, such as local storage, public cloud providers, and more.
Automated rollouts and rollbacks
You can describe the desired state for your deployed containers using Kubernetes, and it can change the actual state to the desired state at a controlled rate.
Automatic bin packing
You provide Kubernetes with a cluster of nodes that it can use to run containerized tasks.
Self-healing
Kubernetes restarts containers that fail, replaces containers, kills containers that don’t respond to your user-defined health check, and doesn’t advertise them to clients until they are ready to serve.
Secret and configuration management
Kubernetes lets you store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys.
I’m focusing on Kubernetes more because other technologies are discussed in my previous blogs in a detailed manner, Do refer them if you are interested in learning some great real-world use cases.✨

Let’s Come to our Project/Task, So what we’re going to build today❓ Excited…
1. Create container image that’s has Jenkins installed using Dockerfile Or You can use the Jenkins Server on RHEL 8/7, When we launch this image, it should automatically start the Jenkins service in the container.

2. Create a job chain of job1, job2, job3 and job4 using build pipeline plugin in Jenkins

3. Job1: Pull the Github repo automatically when some developers push repo to Github.

4. Job2 :

→ By looking at the code or program file, Jenkins should automatically start the respective language interpreter installed image container to deploy code on top of Kubernetes ( eg. If code is of HTML, then Jenkins should start the container that has HTML already installed )

→ Expose your pod so that testing team could perform the testing on the pod

→ Make the data to remain persistent ( If server collects some data like logs, other user information )

5. Job3: Test your app if it is working or not.

6. Job4 : If an app is not working, then send email to the developer with error messages and redeploy the application after code is being edited by the developer

Firstly, We’re going to create Dockerfile for creating the image which will contain pre-installed Jenkins and Kubernetes services as we will need them to create a job chain. Before creating Dockerfile some points to be remembered and noted for prevention from ERRORS!


WinSCP
Use WinSCP software to copy files from Windows to Linux, It uses ssh to transfer the file on port 22.
If you have Kubernetes installed in your System then please copy config, Ca.crt, and client.crt files from your system .kube and minikube folder resp into your workspace where you are going to build Dockerfile.
Make one new directory where you will copy the above files and create a Dockerfile in the same, I have named that directory as workspace.

Run below command to build a Docker image using Dockerfile:-
docker build -t <image name>:<tag name> <path>


Docker Image Created☺
After Image has been created successfully then push that docker image on your Dockerhub account to use it as per your convenience for future purposes or you can use my Docker image for this practical, If you want to skip the steps discussed till now.
Use below commands to push the docker image to Dockerhub:
docker login --username=yourhubusername --email=youremail@company.com
docker tag <docker image ID> <yourhubusername>/<imagename>:<tag name>
docker push <yourhubusername>/<imagename>:<tag name>


The above discussed all parts to be done in Linux OS, Now let’s come to Windows and deploy the Kubernetes cluster using Docker image which is created by us now.
You will need some files to deploy the Kubernetes cluster automatically with just one-click. Refer to my below-given files for the same, Just click on those files they are linked to my Github Repo, Fork the Repo and download codes to perform the task.
kubejen.yml is used to run Jenkins on top of Kubernetes by deploying clusters.
The rg-deployment.yml file used to deploy the Kubernetes clusters by using any image, for E.g. Httpd image used in my project to launch a web server on Jenkins.
The rg-pvc.yml is used to create Volume Claim because if we write anything in our pod and restart the pod, We will lose entire data, Hence we have to attach PVC to retrieve our data even if we delete or restart the pods.
task3.html ← You can use any Html website to host on the webserver, This is just a demo website.
rohitg00/jenkube
Contribute to rohitg00/jenkube development by creating an account on GitHub.
github.com

Now, to run the above files.. you have to run a set of commands given below and before that just start the minikube using minikube start command.

minikube start
kubectl apply -f kubejen.yml

kubectl logs <podname> #To get jenkins password

Jenkins password highlighted
Jenkins runs on port 8080 but I changed the port to 30000 using yml file, So enter your public IP from pod on your browser as <IP:30000>
Enter the password given above and install all recommended plugins and Build Pipeline plugin for future needs.
JOB 1:
It’s a simple configuration to pull GitHub repo automatically in your workspace mounted on Kubernetes pod.


Github Repo

Poll SCM

Shell command to copy files to the workspace
→ The above images are self-explanatory so don’t want to waste time on them, You can refer my previous blogs for the same.

JOB 2:
This is the most important job and will launch the webserver automatically by detecting HTML code inside the pods and launch the HTML container using httpd image.

Automation Job
The job should be triggered only if the previous job has no build failure or it’s stable.

build trigger
Shell command to automatically start containers according to code committed and also should host the same using webserver.

Forgot to attach a screenshot for the right path which is given below
rgpod=$(sudo kubectl get pod -l app=html-webserver -o jsonpath="{.items[0].metadata.name}" )
kubectl cp /ws/*.html  $rgpod:/usr/local/apache2/htdocs
JOB 3:
Simple testing job to check web server is working properly or not.

Build Trigger for Job2

Shell command to check the status of the webserver for testing purpose i.e. If it’s running then skip otherwise pass the failure code to Job 4.

JOB 4:
If WebApp is not working as status received from previous jobs then this Job should send mail to developer with entire build logs for the debugging phase.

Build trigger from Job 3

E-mail Notification post-build action will be used here by Jenkins to send logs to developer.

E-mail notification build action
Now, Let’s create pipeline for monitoring all jobs with beautiful UI because it’s hard to check manually for all teams. I’m here using build pipeline for monitoring purpose, You can use any as per your convenience as there are many to use.🤩

The output from above system
Open build pipeline and set a start as Job 1 and you’ll see all jobs will run automatically and color changes from blue to green. If every job is marked as green then there is no error.
Congratulations! Your System is Bug and Error Free…🎉





https://ghumare64.medium.com/automation-using-kubernetes-from-developer-to-production-system-for-ci-cd-b766ea783e
