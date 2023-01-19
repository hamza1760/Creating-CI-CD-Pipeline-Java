
# Creating-CI-CD-Pipeline-Java

**This is documentation of manual Configuration of virtual machine on mobaXtreme and creating CI/CD Pipeline on Gitlab to automatically build and deploy java project on machine**

 

# Configuring machine and Registering Gitlab-runner on mobaXtreme:

  - Get the credential for a virtual machine using any cloud service like
   AWS.

   - Open mobaXtreme and create a new session . Add the Ip-address and specify username.
  - Generate a keygen of your system by running command 'ssh-keygen' on cmd. 

- Now if you wish to login by keygen on mobaXtreme specifiy the keygen and paste your private key while specifying ip-address and username.

**Gitlab Runner:**
- Gitlab Runner is responsible for doing jobs which are staged inside the pipelines.
- Pipelines are created inside the gitlab so you need a gitlab runner to execute commands that are inside gitlab-ci.yml file.
- Gitlab runner provide you the terminal according to your machine and inside the terminal the commands are executed.
**Installation Of Gitlab Runner Inside MobaXtreme**
- Visit `https://docs.gitlab.com/runner/install/linux-repository.html` and install gitlab runner on your mobaXtreme according to machine.
- After succesfully installing gitlab-runner in your mobaXtreme . You can register your gitlab-runner .
**Registering Gitlab runner with your repository:**
- Go to your gitlab account then setting then CI/CD then runner and expand it and then you can see the runner url and token.
- Open mobaXtrem and write gitlab-runner register and insert your runner url and token when asked. 
- enter any meaning full description and tags and after that select user desired executor . and your runner will be registered succussfully.
- Go to runner page in gitlab expand it and check that runner is registered.
- Configure your runner according to your requirement by clicking the edit icon then you are good to go.
- After Registering your gitlab-runner you can run any gitlab-runner commands on mobaXtreme according to your needs.
- For Java Projects which are build using maven you need to install java and maven inside gitlab runner using **sudo apt install default-jdk** to install java **sudo apt install maven** to install maven.
- Instalations commands can be found any where according to your machine.

# Creating CI/CD PIPELINES ON GITLAB FOR AUTOMATIC BUILDING AND DEPLOYING JAVA PROJECT

## Connecting to Machine With SSH KEY
- open cmd change your directory to desktop and generate a ssh public and private by writing **ssh-keygen**. This will generate ssh public and private key folders at your desktop.
- Open mobaXtreme ,connect to your machine and go inside root/.ssh and create file with name **authorized_keys** if not present. And Paste your public ssh key inside this file.
- Go to your gitlab account repository settings, CI/CD,  and find variable and expand it. Add new variable with name SSH_KEY and paste your ssh private key value in the value field.
- Now you can connect your machine with gitlab with your ssh key.

## Configuration For Creating Pipelines to build and deploy your jar application
- login to gitlab account go to desired repository and go to pipeline and start by adding a new job in your desired branch and this will create .gitlab-ci.yml file your branch. Take a pull of your branch if you want to work locally on this file.

	**gitlab-ci.yml file architecture**
			  
		stages:          # List of stages for jobs, and their order of execution  
	  - build  
	  - deploy  
  
		build-job:       # This job runs in the build stage, which runs first.  
		  stage: build  
		  script:  
		    - echo "Compiling the code..."  
		    - echo "Compile complete."  
		    - echo $CI_COMMIT_REF_NAME  
		    - git checkout -B "$CI_COMMIT_REF_NAME" # this will checkout you to the 	branch where you want to run the pipelines  
			- mvn clean  
		    - mvn package  
		  artifacts:  
		    expire_in: 2 week  
		    when: on_success  
		    paths:  
	      - target/*.jar  # path specified where you want the jar file from  
		  rules:  
		    - if: '$CI_PIPELINE_SOURCE == "push" && $CI_COMMIT_BRANCH == "staging"' # specify your branch in my case its staging  
  
		deploy-job:      # This job runs in the deploy stage.  
		  stage: deploy  # It only runs when *both* jobs in the test stage complete successfully.  
		  before_script:    
		    - cd target  
		  variables:  
		    FILE: "*.jar"  
		  script:  
			     echo "Deploy Code"
		  rules:  
		    - if: '$CI_PIPELINE_SOURCE == "push" && $CI_COMMIT_BRANCH == "staging"'

# Symbolic Link
In [computing](https://en.wikipedia.org/wiki/Computing "Computing"), a **symbolic link** (also **symlink** or **soft link**) is a file whose purpose is to point to a file or directory (called the "target") by specifying a [path](https://en.wikipedia.org/wiki/Path_(computing) "Path (computing)") thereto.

## Creating Symbolic Link In linux To Execute Jar file:
**In this piece of instruction we will learn how make a symbolic link for a jar file and point it to the service file which will be created inside etc/init.d/ and then executing this service file by running the service commands which are "start", "stop", "restart". These commands will effective start stop or restart the jar file.**
- Firstly make sure that you have `<executable>true</executable>` inside pom.xml
inside:

	    <build>  
       <plugins>  
          <plugin>  
             <groupId>org.springframework.boot</groupId>  
             <artifactId>spring-boot-maven-plugin</artifactId>  
             <configuration>  
                <executable>true</executable>  
             </configuration>  
          </plugin>  
       </plugins>  
	    </build>

- **my jar file name is oneapp.jar The jar file I want to link is inside /opt/oneapp/back-office/**
- **I want my service name to be "oneapp-dashboard" which will be created inside /etc/init.d**
- **I will execute the following command to link my jar file with service named oneapp-dashboard**
		
		 `ln -s /opt/oneapp/back-office/oneapp.jar /etc/init.d/oneapp-dashbaord` 
- **Few more commands to giving rights to the service and for changing ownership of the jar need to be executed to make things work without any issue.**
	 - sudo chmod +x /etc/init.d/oneapp-dashbaord  (**give executive rights to jar**)

	- sudo chown root:root /opt/oneapp/back-office/oneapp.jar (**give owner rights to the jar file**)

- **after running all these commands your jar will be linked to service named oneapp-dashboard and can be executed by using the start , stop and restart commands.**
		

    -  etc/init.d/oneapp-dashboard start (**This will start the service**)
    - etc/init.d/oneapp-dashboard stop(**This will stop the service**)
    - etc/init.d/oneapp-dashboard restart(**This will restart the service**)
		
   
