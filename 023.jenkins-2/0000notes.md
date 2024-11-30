# Lecture-2 jenkins

## Job :: Steps To Create Jenkins Job with Git Repo + Maven + Tomcat Server



1) Tomcat Server Setup in Linux VM

2) Install "Deploy To Container Plugin" in Jenkins

    Go to Jenkins Dashboard -> Manage Jenkins --> Manage Plugins -> Goto Available Tab -> Search For "Deploy To Container" Plugin -> Install without restart.

3) Create Jenkins Job (Free Style Project)

		-> New Item
		-> Enter Item Name (Job Name)
		-> Select Free Style Project & Click OK
		-> Enter some description
		-> Go to "Source Code Management" Tab and Select "Git"
		-> Enter Project "Git Repo URL"
		-> Go to "Build tab"
		-> Click on Add Build Step and Select 'Inovke Top Level Maven Targets'
		-> Select Maven and enter goals 'clean package'
		-> Click on 'Post Build Action' and Select 'Deploy war/ear to container' option
		-> Give path of war file (You can give like this also : **/*.war )
		-> Enter Context Path (give project name Ex: java_web_app)
		-> Click on 'Add Container' and select Tomcat version 9.x
		-> Add Tomcat server credentials 
				(give the username & pwd which is having manager-script role)
		-> Enter Tomact Server URL (http://ec2-vm-ip:tomcat-server-port)
		-> Click on Apply and Save


4) Run the job now using 'Build Now' option and see see 'Console Output' of job

5) Once Job Executed successfully, go to tomcat server dashboard and see application should be displayed.

6) Click on the applicaton name (it should display our application)

