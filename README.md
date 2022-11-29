# Java-App-Service-CI-CD-using-Maven-and-VSTS

In this example we will be creating a continuous integration  and continuous deployment for a Java application to Azure App Services using Visual Studio Team Services (VSTS) & Apache Maven.

## Deploy and Configure an Azure Web App

First we'll create an Azure Web App for us to depploy our Java Code to later.

Navigate to the [Azure Portal](portal.azure.com)

Click the **+** icon to navigate to the new resources creation tab.

In the "Web + Mobile" tab, select the Web App option.

Enter a unique `App Name`, Select the appropriate `Subscription` and `Resource Group`.

Create a basic App Service Plan for scaling your web app and click **Create**.

<img src="content/img1.png" width="400">

Once the Web App has been successfully created navigate to the **Deployment Credentials** tab inside the Web App options. This will be used to publish files to the server directory.

Create an `FTP/deployment username` and `password` and **Save**.

<img src="content/img2.png" width="600">

Navigate to the **Application Settings** tab.

Turn Java on by selecting the `Java Version`: Java 8. Use **Tomcat 8.5.6** as your Web Container.

**Save** the Web App Settings.

<img src="content/img3.png" width="800">

## Create a VSTS code project

Navigate to your [Visual Studio Profile](https://app.vsaex.visualstudio.com) and sign in.

> To create a visual Studio account follow the documentation [here](https://www.visualstudio.com/team-services/).

Select a team and create a **New Project**.

<img src="content/img4.png" width="800">

Name your Project. Select **Git** as your version control. Click **Create**

<img src="content/img5.png" width="800">

Once the project has bee8 created, Clone the repository locally using the repo URI.
Push the contents of the sample Java App in this example or your own. Be sure to include a POM.xml file for Maven to build your application.

<img src="content/img6.png" width="800">

On your local machine use the following git commands:

``` Powershell

PS C:\> git init
PS C:\> git remote add origin https://<teamname>.visualstudio.com/_git/<projectname>
PS C:\> git add .
PS C:\> git commit -m "Working Java App"
PS C:\> git push origin master

```

Once your initial commit is completed, navigate to the **Code** tab on the top menu. Your code will be viewable now in VSTS.

<img src="content/img7.png" width="800">

## Create a Maven build definition in VSTS

In this step we will create a Build definition in VSTS that compiles our java code using Apache Maven. We will also configure the build tasks to be trigger continuous with commits so a build in processed for all repo commits.

Navigate to **Build & Release** in the top menu.

Select an empty Build Definition template.

<img src="content/img8.png" width="800">

Add the following tasks to your template. Use the default settings
* `Get Source` - This pulls the code repo from your targeted repository. By default it will be auto configured for the project repo.
* `Maven pom.xml` - This  is the Project Object Model that compiles your java app's dependencies
* `Publish Artifact` - this uploads the artifacts from the build to a drop directory to be deployed to your web app.

<img src="content/img9.png" width="800">

For more Maven build options go [here](https://www.visualstudio.com/en-us/docs/build/steps/build/maven).

Navigate to the **Triggers** tab for the Build Definition Template.

Enable **Continuous Integration** and connect it with your project master branch. This will trigger the build to start for all commits.

<img src="content/img10.png" width="800">

Click **Save & Queue** to test your build.

<img src="content/img11.png" width="800">

You will see a log from the Agent that built your code. If any errors are generated extend the point of failure to debug.

## Create an App Services release definition in VSTS

Now that we have a build definition, we will generate a release definition the deploys our successful builds to our Web App in Azure.

Navigate to the **Releases** tab and select **New Release Definition**.

Select the **Deploy to Azure App Service template**.

<img src="content/img12.png" width="800">

In the definition select the `Azure Subscription` and the `App Service Name` of your web app you created earlier.

For the `Destination or Folder` setting **Browse** to the `hello.war` file that was generated from the Build.

Click **OK**.

<img src="content/img13.png" width="400">

Navigate to the **Triggers** tab in the Release Definition.

Enable **Continuous Deployment** and link the Build Definition you previously created as your artifact source. This will configure your release to be triggered by a successful Build definition.

<img src="content/img14.png" width="800">

Finally *Save* and click **Queue Release**.

## Validating Web App Deployments

Navigate to the Azure portal and click your Web App URL. You will see this in your web app overview tab.

<img src="content/img15.png" width="800">

If the Release was successful you will see the sample Java App being displayed.

<img src="content/img16.png" width="600">

---
If you were unsuccessful verify that your Release Definition was successful in VSTS. Otherwise navigate to the site's Kudu Dashboard [yourwebappname].scm.azurewebsites.net 

<img src="content/img17.png" width="800">

Navigate to **Deployments** and you are able to see the which deployments were successful with the status code and Buil/Release ID.

<img src="content/img18.png" width="800">

Another common fix would be to navigate to the **Debug Console** in the Kudu Dashboard and rename your Hello.war file to ROOT.war usually found in this directory - D:\home\site\wwwroot\webapps> 

<img src="content/img19.png" width="800">
