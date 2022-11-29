# DevOps info
DevOps-related info

---

## OS
- The Kernel loads first and manages the HW of the pc, using device drivers.
- File system management: Mac and linux has one root dir. Windows has multiple roots.
- OS manages users and permissions for the computers.
- OS assigns ports and ip addresses.
- Different distros are on the application level of the pc.

---

## VM
- Creating Virtual Machine Images (snapshots) is important in order to save all of your data in case of failure.
- VM has it’s own network, so the host can’t see it in it’s network by default.

---

## Linux Filesystem Hierarchy
- Every user will have it’s own space in /home dir, and root will have /root dir.
- /bin - binaries available for OS and all users.
- /sbin - binaries which needs sudo privilege.
- /lib directories - libraries for the executables.
- /usr - has also /bin and /sbin directories and they are the ones actually executed. Also has /local directory - this path /usr/local/bin will be where the user installs apps.
- /opt - apps which do not need other libraries (like /bin uses /lib dirs), so the app will be in one folder.
- /boot - booting the system (don’t touch).
- /etc - configuration files.
- /dev - devices connected to the system.
- /var - OS and apps logs.
- /tmp - temporary files (will be deleted).
- /media - external media (usb and such).
- /mnt - manually mount file systems.
- Hidden files - starts with a dot (dotfiles).

---

## Linux Commands
- ls -R = list recursively
- uname -a = complete info about the computer
- cat /etc/os-release = info about the linux distro
- lscpu / lsmem = info about the hardware (cpu, memory…)
- sudo adduser <username> = add new user
- sudo useradd = same but available in all linux and can get params
- cat /etc/passwd = list all users: <username>:<pass>:<uid>:<gid>:<gecos>:<homedir>:<shell>

---

## Package Manager
- The manager installs packages for us in the correct dirs (bin, etc,...)
- /etc/apt/sources.list = list of all of the sources for repositories that the package manager uses
- If what we want to install is not available through PM - We can install with “snap” PM. But with it, the dependencies aren’t shared! With apt we share the dependencies using /lib. We could also add to the /etc/apt/sources.list (we add PPA’s to the list).
- Debian based (ubuntu,debian,mint) uses apt,apt-get . Red Hat based (RHEL, CentOS, Fedora) uses yum,dnf

---

## Linux User Management
- There are superuser account, user account, service account for encapsulating the user privilege to only what they need to do.
- We’ll manage that with groups of users.
- Change user group = sudo usermod -g <groupname> <username>
- Change ownership of files = sudo chown / sudo chgrp
- When we add a user to a remote server, we'll need to add the public key to the user's ```/home/<username>/.ssh/authorized_keys``` file in order for the user to be able to ssh to the machine as it's own user and not as root

---

## Environment Variables
- Print all env vars = printenv 
- export <var>=<val> for current session
- Edit ./bashrc and run ‘source .bashrc’ for all sessions of the user
- Edit /etc/environment for system-wide configs

---

## Networking
- Devices connected to the same LAN are connected via switch (which the router acts as also) - The switch can route traffic between these devices
- When trying to access a device outside of the LAN we need a router for WAN connections
- IP of the router is Gateway
- The router knows if the device is the LAN based on the subnet (start IP and subnet mask that could be written as a CIDR block)
- NAT (Network Address Translation) will allow us to use a chosen public IP for WAN connections without exposing our LAN's private IP addresses
- There is SNAT(Static NAT), DNAT (Dynamic NAT) and PAT (Port Addressing Translation) which is the most widely used. In it - the router keeps a NAT port translation table for access to the LAN devices
- Firewall will consist of firewall rules which describe which IPs and ports are allowed to a specific IP address in our network
- DNS heirarchy consists of: Root domains (.) TLDs (.edu,.com,.gov,.us,.uk,...), Domain Name (mywebname)
- ICANN manages all of the domain names
- We can use subdomains for the domain we have - www.courses.mywebname.com , www.business.mywebname.com
- When trying to access a domain - The DNS client installed on our system checks with the Resolver if the IP of the domain is cached, if it doesn't it will recursively check with the Root Domain servers and so on... When the resolver has the answer IP it will cache it and return it to our DNS client
- Info can be displayed with ifconfig, netstat (for active listening processes), psaux (check process resources and ports), nslookup (for getting IP address of a domain, or the other way around), ping (check if IP is accessible), telnet client (check if a port is accessible)
- Wireshark can be used to check and record network communication
- Connection with SSH key pair: the client creates an SSH key pair, the public key is installed on the remote server (authorized_keys) and then the client can authenticate and communicate with remote server. The remote server will be added to the client's known_hosts file

---

## Build Tools
- We need to build our code to a single artifact and then we can move it and deploy to our servers from an artifact repo
- When creating Maven/Gradle project in java, the build tool will download dependencies for the project based on requirements files in it. If we need more dependencies, we'll add them to that file (for example pom.xml for maven projects)
- After getting the artifact to our servers - we can run ```java -jar <artifact_path>``` in order to run the app

---

## Artifact Repository Manager (Nexus examples)
- Artifacts are apps built into a single file (different formats) which can be shared. An artifact repo is where these files can be shared
- An artifact repo is file format specific, so if we're developing alot of technologies we need separate repos. That's why we need an artifact repository manager, like Nexus or JFrog
- An example for a public repo manager is ```Maven Central Repository```, Which stores jar files for the public's use when developing with Java. Also ```npm``` for JS
- Repo managers have a flexible REST API which is good for our CI/CD pipeline integration when wanting to upload/download an artifact
- Repo managers have cleanup policies for the repo in order to save storage
- When extracting Nexus download there are 2 dirs: ```nexus``` dir for the runtime of Nexus and ```sonatype-work/nexus``` for the configurations and data (the uploaded files)
- There are 3 types of repositories in Nexus: **proxy** - proxies the main repo specified and caches data. **hosted** - primary storage for the company's artifacts. **group** - can group together proxy and hosted repos for the developers to use only 1 url instead of accessing all of the repos for their dependencies
- The connection to Nexus will be added in the config files of the project ```build.gradle```/```pom.xml```. Then we can run a build: ```gradlew build```/```mvn package``` and we will have the artifact created. After that we will publish with ```gradlew publish```/```mvn deploy``` to publish to our Nexus repo
- Note Maven project credentials file ```settings.xml``` should be created under .m2 folder
- Nexus API query example: ```url -u user:password -X GET 'http://<nexusIP>:<nexusPort>/service/rest/v1/repositories```
- When creating a new repo we assign it to a blob store that we can create depending on our storage
- The components in each repo includes 1 or more assets. **Docker assets are called layers and can be shared through images (components)**
- Cleanup policies for the repos does **not** delete the blob storage of the components until compact-blob task is run

---

## Containers (Docker examples)
- A container is a portable artifact which has the app's configs and dependencies included. The container has it's own isolated environment so it doesn't matter on which OS it is running
- Containers are stored in a container repo (private/public). Default is Dockerhub
- Containers consists of layers, when the base layer is most of the times a linux OS base image (small sized)
- Containers use the host's kernel and only virtualizes the application layer of the OS. VM's boots up it's own OS kernel
- ```docker logs <containerId>``` - outputs the log of the container
- ```docker exec -it <containerId>``` - access the container terminal
- ```docker network ls``` - lists all of the networks for connections between containers
- docker compose takes care of creating/deleting the common network for our containers when running ```docker-compose up/down```
- We can set env variables either in the Dockerfile or as external env variables from the command line. Depending on whether they change from run to run or stay fixed
- ```RUN``` lets us run linux commands in the container
- ```CMD``` creates an entrypoint for the container
- ```docker build -t my-app:1.0 .``` - builds a tagged image based on Dockerfile
- For CI integration, we will commit our code **with the Dockerfile** and then the CI tool will automatically build the image and push it to a remote repo
- In AWS ECR the repositories are per-image
- Pushing an image to ECR: 1. login to remote repo account using ```docker login``` (for aws connection first set up the configuration with ```aws config```) 2. build the image locally. 3. tag the image using the registry domain, image name, and tag: ```docker tag my-app:latest <registry_url>/my-app:latest```. 4. push to remote: ```docker push <registry_url>/my-app:latest```
- Deploying the application in the server: 1. login to remote repo account using ```docker login```. 2. configure where the image resides on the remote server in order for the appropriate service to be able to pull it.
- Volumes: there are 3 types of docker volumes: 1. specifying a directory which is available on the host machine and connecting it to a path in the container. 2. anonymous volumes. 3. named volumes (this way docker is handling the storage on our host): ```<volume-name>:<path-in-container>```. Windows: **C:\ProgramData\docker\volumes** Linux/Mac: **/var/lib/docker/volumes**
- When running for example a Nexus container, it already creates a nexus user for us and runs nexus with this user out of the box, so we don't need to take care of these aspects. This is not the case with every image, and we have to verify that the app is ran by non-root user
- ```docker inspect <volume name>``` - details about the volumes and it's physical location on the server
- Best practices: 1. Don't use base images for your Dockerfile. 2. Use specific versions of the images. 3. Use light base images. 4. Optimize caching image layers, by copying the requirements file of the project and running install and only then copying all of the rest of the code. This way - if nothing changed for the installation and only the code was modified - then the installation layer is cached. 5. Use .docker-ignore file for excluding files which don't need to be transfered to the container. 6. Scan your images for vulnerabilities with ```docker scan <image-name>```. 7. Use multi-stage build for getting rid of unnecessary artifacts and build tools in your final image. This is done by specifying copy artifacts from the previous build. example:
```Docker
# stage 1
FROM maven AS build
WORKDIR /app
COPY myapp /app
RUN mvn package

# stage 2
FROM tomcat
COPY --from=build /app/target/file.war /usr/local/tomcat/web/file.war
```
- If we need the host's docker to be available on our container we can run the container with volumes to mount the docker directories into our container:
```bash
docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker jenkins/jenkins:lts
```
- Note that every time that docker is restarted, we need to change the permission to ```/var/run/docker.sock``` file to enable jenkins user inside the container to be able to read and write to it

- If we want to push an image to an insecure private repo we need to add the ip,port in ```/etc/docker/daemon.json``` file and restart docker

---

## Build Automation & CI/CD (Jenkins)
- We need to expose 2 ports when starting jekins container - 8080 and 50000 for cluster communication
- For testing and building our applications we need for example npm, maven, and such in our jenkins. We can install as plugins (and then build tools in the UI) or directly on the jenkin's server. The main difference will be in the flexibility
- When creating jenkins job which fetches code from git, it will be stored under workspace folder of that build
- Build and push to container repo using credentials secret as variables:
```Dockerfile
docker build -t <repo_user>/java-maven-app:jma-1.0.0 .
echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
docker push <repo_user>/java-maven-app:jma-1.0.0
```
- Environment variables which are available for use in our pipelines can be shown by accessing the jenkins url with ```<jenkins-url>/env-vars.html```
- We need to explicitly add the build tools that we need in the pipeline, using ```tools``` attribute
- Replay option lets us rerun the build with changes to the Jenkinsfile and groovy scripts without changing our source code
- Jenkinsfile example:
```jenkins
def gv

pipeline {
    agent any

    parameters {
        choice(name: 'VERSION', choices: ['1.0.0', '1.1.0', '1.2.0'], description: '')
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }

    tools {
        maven 'maven-3.6'
    }

    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage("build") {

            steps {
                script {
                    gv.buildJar()
                }
            }
        }
        stage("test") {

            when {
                expression {
                    params.executeTests
                }
            }

            steps {
                echo 'testing the app'
            }
        }
        stage("deploy") {

            input {
                message "Select the environment to deploy to"
                ok "Done"
                parameters {
                    choice(name: 'ENV', choices: ['dev', 'staging', 'prod'], description: '')
                }
            }

            steps {
                echo "deploying the app to environment: ${ENV}"
                echo "deploying version ${params.VERSION}"
                // withCredentials([
                //     usernamePassword(credentials: 'dockerhub-credentials', usernameVariable: USER, passwordVariable: PASSWD)
                // ]) {
                //     sh "some script ${USER} ${PASSWD}"
                // }
            }
        }
    }
    post {
        always {
            echo 'end of pipeline'
        }
        failure {
            echo 'status: FAILURE'
        }
        success {
            echo 'status: SUCCESS'
        }
    }
}
```
- Multibranch pipeline in jenkins will scan all of the matching branches and search for Jenkinsfile. If none is found - the branch is skipped. Then we can define conditions in the jenkinsfile, defining what needs to be done for each branch using the predifined BRANCH_NAME parameter
- There are 3 levels of credentials - System which will be only used by Jenkins itself, Global which is visible in all jobs, and credentials created in Multibranch pipelines which will be available only for these pipelines
- Jenkins shared library will allow us to share pieces of code across some jobs in jenkins so we won't repeat stuff and can make mistakes. We will create a groovy project and in it's ```var``` dir we will have all of our shared functions. This will reside in SCM tool like gitlab and we will add it to our jenkins using the ```Global Pipeline Libraries``` in manage jenkins
- In order to enable Github/Gitlab webhooks for SCM pooling we need to provide an API token to Jenkins and configure in them the jenkins integration in order to notify when a change has happend in our project. In order to use the multibranch pipeline with webhooks we'll need to install a plugin
- Versioning: We can use build tools in order to make our app versioning incremental, for example in mvn project: 
```
mvn build-helper:parse-version versions:set -DnewVersion=${parsedVersion.majorVersion}.${parsedVersion.minorVersion}.${parsedVersion.nextIncrementalVersion} versions:commit
```
This command will increment the patch/incremental version of the app
- After the increment, we need to push the file which has the version number to git. We will do this as part of the build's stages, but we will need to exclude the author jenkins to allow triggering of another pipeline when pushing this change. This will be done using the ```Ignore Committer Strategy``` plugin in jenkins

---