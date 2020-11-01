Building pipleine for your containers

This lab demonstrates Azure Git pipeline, which provides the ability to do continuous integration/continuous delivery (CI/CD) from within your Github projects.The lab will lead you through the anatomy of how Azure pipeline solve this problem.
Build pipeline for Kubernetes usually contains the steps that will fetch the code, build the Docker image, push that image to your Docker repository and then publish the artifacts and deploy them into AKS.

1. Create service connection Docker registry - 

   Login into azure account and navigate to project created by you. Go to project setttings bottom of the page. 

![project](project.png) 

2. Click on Service connections and select new service connection.

 ![sc1](sc1.png) 

3. Search Docker Registry and select

 ![sc2](sc2.png)

4. select register type, select Docker hub for public docker image

![sc3](sc3.png) 


5. Create new pipeline and select code base repos and tool which is being used for building the project. In our case, Gradle is being used to build the project. 

![pipeline](pipeline.png) 

6. select a source

![pipeline2](pipeline2.png) 

7. select code base

![codebase](codebase.png) 

8. select a repository
![repository](repository.png) 

9. Configuring pipeling
![ConfigurePipeline](ConfigurePipeline.png) 


5. Provide a Project name and select the Visibility of the project.  Click on Create project.
Once our project is ready, we can use Azure DevOps services for CICD implementation.

6.	Click on Repos. We will need application code to operate on it, you can generate credential which can be used while performing GIT operation like Push, pull etc.
![repo](repo.png) 

Note – Please store the password somewhere as a reference which can be used throughout workshop.

7.	Below is the command which can be used to set remote git URL before pushing into Azure GIT.
It will be applicable lab 1st onwards.

git remote add origin https://RupchandDewangan2@dev.azure.com/RupchandDewangan2/Pages/_git/Pages

8.	If due to any reason, if you want to delete the organization, then follow steps.

Go to project settings at the bottom of the page and search for delete organization 

![settings](settings.png) 

![delete1](delete1.png) 

![delete2](delete2.png) 

Once delete, you won’t be able to use it until re-store.
![restore](restore.png) 


