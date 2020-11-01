Building pipleine for your containers

This lab demonstrates Azure Git pipeline, which provides the ability to do continuous integration/continuous delivery (CI/CD) from within your Github projects.The lab will lead you through the anatomy of how Azure pipeline solve this problem.
Build pipeline for Kubernetes usually contains the steps that will fetch the code, build the Docker image, push that image to your Docker repository and then publish the artifacts and deploy them into AKS.

1. Create service connection Docker registry - 

   Login into azure account and navigate to project created by you. Go to project setttings bottom of the page. 

![sc2](sc2.png) 

2. Click on Service connections and select new service connection.

![sc2](sc2.png) 



