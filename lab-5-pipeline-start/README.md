Building pipleine for your containers

This lab demonstrates Azure Git pipeline, which provides the ability to do continuous integration/continuous delivery (CI/CD) from within your Github projects.The lab will lead you through the anatomy of how Azure pipeline solve this problem.
Build pipeline for Kubernetes usually contains the steps that will fetch the code, build the Docker image, push that image to your Docker repository and then publish the artifacts and deploy them into AKS.

1. Create service connection Docker registry - 

   Login into azure account and navigate to project created by you. Go to project setttings bottom of the page. 

![project](project.png) 

2. Click on Service connections and select new service connection.

![sc1](sc1.png) 

3. Search Docker Registry and select.

![sc2](sc2.PNG)

4. select register type, select Docker hub for public docker image.

![sc3](sc3.PNG) 

5. Create new pipeline and select code base repos and tool which is being used for building the project. In our case, Gradle is being used to build the project. 

![pipeline](pipeline.png) 

6. select a source.

![pipeline2](pipeline2.png) 

7. select code base.

![codebase](codebase.PNG) 

8. select a repository.

![repository](repository.PNG) 

9. Configuring pipeling.

![ConfigurePipeline](ConfigurePipeline.PNG) 

10.


