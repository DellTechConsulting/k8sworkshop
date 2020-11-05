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

10. Azure generates YAML script to build the project

![azure-pipeline](azure-pipeline.PNG) 

11. Add below lines which helps to build docker images and push into docker hub

![DockerTask](DockerTask.PNG)

Complete YAML from Gradle buidl to Dockerized and deploying into AKS

# Gradle
# Build your Java project and run tests with Gradle using a Gradle wrapper script.
# Add steps that analyze code, save build artifacts, deploy, and more:
# https://docs.microsoft.com/azure/devops/pipelines/languages/java

trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: Gradle@2
  inputs:
    workingDirectory: ''
    gradleWrapperFile: 'gradlew'
    gradleOptions: '-Xmx3072m'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '1.11'
    jdkArchitectureOption: 'x64'
    publishJUnitResults: true
    testResultsFiles: '**/TEST-*.xml'
    tasks: 'build'
- task: Docker@2
  displayName: Login to Docker Hub
  inputs:
    command: login
    containerRegistry: dockerRegistryServiceConnection
- task: Docker@2
  displayName: Build and Push
  inputs:
    command: buildAndPush
    repository: rupchand1006/pages
    tags: |
      config
- task: Kubernetes@1
  displayName: 'kubectl apply'
  inputs:
    connectionType: 'Azure Resource Manager'
    azureSubscriptionEndpoint: 'MSDN Platforms Subscription (79d8ffb4-b8c9-405e-acf9-aec0ebdc6f1e)'
    azureResourceGroup: myResourceGroup
    kubernetesCluster: myAKSCluster
    namespace: rupchand
    command: apply
    arguments: '-f $(Build.SourcesDirectory)/deployment/pages-namespace.yaml -f $(Build.SourcesDirectory)/deployment/pages-service.yaml -f $(Build.SourcesDirectory)/deployment/pages-config.yaml -f $(Build.SourcesDirectory)/deployment/pages-deployment.yaml'
    containerRegistryType: 'Container Registry'
    dockerRegistryEndpoint: dockerRegistryServiceConnection
    forceUpdate: false
  condition: succeededOrFailed()
              


