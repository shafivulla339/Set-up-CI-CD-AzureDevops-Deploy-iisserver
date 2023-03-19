Azure DevOps CI/CD Pipeline to deploy .net application onto an IIS server
Overview
To publish a .net web app to a IIS server in an azure virtual machine. The main objective of this project was to configure, build and release pipelines to deploy the Windows (Windows Server 2016 Datacenter).

1.Setup a Project in Azure Repos:
Azure Repository is a set of version control tools that we can use to manage our code. In case if we are entirely new to version control, then version control enables us to track changes we make in our code over time. There is so many software that is available in the market to enable version control on our code.


    I have create the Project name of Practice-pipeline , and crate the repository name is MywebApp . and I have commit the code into the Azure repos. 
URL: https://dev.azure.com/shaikshafivulla0734/Practice-Pieplines/_git/MyWebApp

![image](https://user-images.githubusercontent.com/99337360/226182779-0447abd4-6f9b-4a9b-a68b-9a03c81345f1.png)


 
2.Setup the build pipeline:
      Two ways to setup the build pipeline. We can click on setup build, which will provide us most relevant build templates per the source and create the pipeline with YAML.
        Else we can go from the scratch by selecting pipelines → new pipeline → use the classic editor. We need to select the version control, project, repository and the branch to trigger the build. Providing the required data will take you to the editor to select a template.

I have Build the pipeline using YAML script.
 
Now we can Select the Repository to Build the pipeline

 



Now I am selecting the ASP .NET Build template as per source requirement.
 
Now we will get the azure pipeline and if any modification required need to modify and click on the save and run button .


![image](https://user-images.githubusercontent.com/99337360/226183183-5088d613-b569-4911-aa98-574c04017672.png)



Now I have Added the PublishBuildArtifact Steps mention below.

- task: PublishBuildArtifacts@1    
  displayName: 'Publish Artifact: drop'
  inputs:
    PathtoPublish: '$(build.artifactstagingdirectory)'





 


Select the Triggers to view the option to enable continuous Integration here we check the box to enable it and in the branch filters we have two options Exclude and Include in our case we will include the branch whenever new commits are done.
 
Now we can manually trigger the pipeline and watch the pipeline work.

![image](https://user-images.githubusercontent.com/99337360/226183213-a26eb184-3a3b-44d5-911b-d13b33e226fb.png)

 
Now we can check the Artifact Published or not.  
 
 ![image](https://user-images.githubusercontent.com/99337360/226183274-3d71065e-d8ba-4b1e-9a3c-e1874c935631.png)


Step 2: Creating a Azure Virtual machine with Azure cloud:

          Sign in to the azure portal always create your resource group first in the region where deployment is gone be done. In the search tab search for the virtual machine.  Here we are going to create Windows Server 2016 VM for our deployment.
 
![image](https://user-images.githubusercontent.com/99337360/226192274-a387de1e-aec6-4863-ad94-9ebe0eb29534.png)

Navigate to the Virtual machine resource → overview. Here you will see the analytics of your VM for the selected duration.
 
![image](https://user-images.githubusercontent.com/99337360/226192251-2d8225e8-3ac9-496b-95ab-4f60e68a67c5.png)


Step 4: Setup IIS in azure virtual machine:
After login into the VM open the server manager
search for server manager → Add roles and features → Next (Before you begin) → Installation Type (Role based or feature based) → Server selection (Select server from pool) → Server Roles (Enable Web Server (IIS)) → Features (Enable .Net Framework 4.6) → Web server Roles → Role Services (Enable IIS 6 Management compatibility)
 
 ![image](https://user-images.githubusercontent.com/99337360/226192225-976edffb-dd7c-47d0-a7aa-cff819fcc464.png)

 
Step 5: Setup release pipeline:
In order to deploy the web application to IIS we should create a deployment group and provision a service agent in our windows server 2016 in the virtual machine.
•	Deployment group:
A deployment group represent the environment that the application will be deployed to. It is a set of target machines each having a service agent installed.
Navigate to pipelines → deployment groups in azure DevOps. Click on +New to create a new deployment group.

![image](https://user-images.githubusercontent.com/99337360/226192193-b6e9a7d2-a178-4078-84bc-1e9ea063e5dd.png)

 
Now we need to install the service agent into the target machine. To install, the generated script must be executed in the server PowerShell as an admin. To authenticate the script, we must attach the personal access token of the user to the script. DevOps will create a token and handle that for you if you check the check box highlighted in the following image. If the token is attached, we are not prompted to enter the authentication type and the credentials. Else will have to provide the type and the credentials.

![image](https://user-images.githubusercontent.com/99337360/226192158-44991a9d-353a-4e51-979f-17842b4f9a05.png)

 
As mentioned in the above figure, while registering the agent it will prompt for several requirements.
•	Enter replace →y
•	Group tags → y
•	Add tags → dev, prod (to specify the scope the deployment group will be handling we using tags)
•	Add credentials we used while creating the vm as we are logged in to the vm through that account.
Now check the deployment groups in azure DevOps. You will see the following. The agent is installed and the group is active.

 ![image](https://user-images.githubusercontent.com/99337360/226192120-aef67c47-1b3a-4934-986a-b9474b1a0729.png)


Step 6: Setup release pipeline:

![image](https://user-images.githubusercontent.com/99337360/226192114-f955da53-6c50-40c8-aefe-f8dbfa2f65c3.png)

 
We are almost done now. Go to releases → new release if no prior releases. Follow the above images. Will explain after the figures.
1.Select the Build Artifact to Release and configure the project and build pipeline and default version as a latest 
2.Enable the Triger continues deployment
3.setup the deployment configuration and select template as a ASP.NET web application deploy on IIS Server in The VM.

![image](https://user-images.githubusercontent.com/99337360/226192090-b3519e64-825f-4237-a0e5-e5e264b19307.png)

 
IIS Deployment Need to add the Deployment Group already we have configured.
 
![image](https://user-images.githubusercontent.com/99337360/226192071-6c8cb8bf-b64f-4276-a6d9-7bafc61df662.png)



Now we need to select the IIS Web App Deploy Give the physical path of the Package.

![image](https://user-images.githubusercontent.com/99337360/226192054-027bf740-e011-42b6-a21a-ff2928e55ec7.png)

 
Navigate to click on the new release Pipeline. click on the logs button to view the logs. Successful deployment will show the below figure.
 
![image](https://user-images.githubusercontent.com/99337360/226192012-2311e090-0697-4f8f-8471-e7df5b80931a.png)

![image](https://user-images.githubusercontent.com/99337360/226192030-22ddf379-df68-43ce-9e97-d70fdca811fd.png)



 
As the final step you can navigate to the iis server in azure vm by going to server manager → iis → select server and right click → select iis manager and go to the site deployed and browse.

![image](https://user-images.githubusercontent.com/99337360/226191961-f6f19609-7980-4301-bdae-415c4675bd83.png)

 
DNS Configuration:
DNS - we have setup azureiisserver.eastus.cloudapp.azure.com to point to the public IP of our VM.
 Setup a website:
To begin, open up IIS manager and create a new website to use as your reverse proxy end-point. It should look like this

Right click on Sites, then select Add Website.

![image](https://user-images.githubusercontent.com/99337360/226191901-eddb6e33-e432-42e5-8de6-4c3dc9f34e33.png)

 
Next, fill in some details about the website. For our example, we will create a new sub-domain for our azureiisserver.eastus.cloudapp.azure.com website.
  Note: Even though we aren't setting up an actual website, we still need to create a folder somewhere for our dummy site. Here, we created a test folder under our websites folder.
Once you have completed the form, click OK.

![image](https://user-images.githubusercontent.com/99337360/226191878-c6378ded-1b5e-4645-99d3-5e1b14b7f8a4.png)

 
After clicking OK, we should see our website.
When selected, we should see the following options. Notice the URL Rewrite option that shows up after we successfully installed the extension. This is what we will use to configure our reverse proxy.

![image](https://user-images.githubusercontent.com/99337360/226191854-741ebe9c-9547-4e55-8ab3-a603f0e62b2a.png)

 
Step 3: Configure URL Rewrite
After we have setup our new website, that will act as our public end-point, we need to configure it as our reverse proxy.
To do this, double click on the URL Rewrite option under our website (shown in Figure)

![image](https://user-images.githubusercontent.com/99337360/226191827-8e42e205-b623-4daa-9867-77b6f6ec0c94.png)

 
Next, click the Add Rule(s) item from the Actions section on the far right.
This will bring up the following dialog, under Inbound rules. Select Blank rule and click OK.

![image](https://user-images.githubusercontent.com/99337360/226191807-fdfe62f8-d57f-48b6-bffe-1e8a42b676de.png)

 
For our example, the configuration is pretty straightforward. It's outside of the scope of this article, but know you can get more complex in the pattern matching and route handling.
First, we need to provide a name for our rule, add a pattern for how we want to match incoming request, and set the rewrite URL.

![image](https://user-images.githubusercontent.com/99337360/226191750-1705b364-f5e9-49b9-bec5-23feed1a3fc2.png)

![image](https://user-images.githubusercontent.com/99337360/226191770-d21ca941-c86b-4411-a3d0-8caa563c4cc4.png)

 
Breaking this step down a bit further, the pattern is how we want to handle capturing incoming request to the website we setup to act as our reverse proxy (azureii-test.eastus.cloudapp.azure.com
Since we want to be very broad and capture everything, our regex is quite simple. We want to match everything after the domain name, which is what ".*" will do for us.
Click the Test pattern... button and a tool dialog pops up that allows us to test our pattern. We want to make sure that anytime someone visits azureiisserver.eastus.cloudapp.azure.com/defult.html, we capture anything after the domain name. This way we ensure when we rewrite the URL, we can point to the right location.
Take note of the capture group {R:0}. We will use this to configure our rewrite URL.

![image](https://user-images.githubusercontent.com/99337360/226191710-8c770f02-938f-4b34-a485-ca360fed40dd.png)

 
Taking a closer look at our action section, we see the Rewrite URL input field. This field indicates where we want our website to point. Since we want to route back to our website, we put in the URL and append the regex capture group {R:0} from the previous test pattern.
This will forward any request coming into our reverse proxy to the origin server tevpro.com keeping the URL fully intact.
 
 ![image](https://user-images.githubusercontent.com/99337360/226191687-9df999fc-8d86-47fb-b86f-b9b2a5ec565d.png)


Once you have finished setting up the new rule, click Apply.
We have configured our new website to act as a reverse proxy.



Step 4: Test the Reverse Proxy:
Now that we have completed the reverse proxy configuration, we need to test our new website.
We expect that anytime a user visits the URL, azureii-test.eastus.cloudapp.azure.com, we'll automatically reroute the corresponding request to azureiisserver.eastus.cloudapp.azure.com.
As you can see below, when we type the URL into our web browser, we see the http://azureiisserver.eastus.cloudapp.azure.com/ website. It is actually being masked behind our reverse proxy website. Pretty cool, right?
 
![image](https://user-images.githubusercontent.com/99337360/226183320-5f8e5c9a-ba9f-4b10-a248-a1c333b1296c.png)




