LAB 8 

Follow steps provided under MASTER

REPO    pages-persistence-8
git		kjaixqtl3jfchzzo35bcenlggkri76lkfc7d3mkh5mj5agghue3a
wget  https://dell-edu-lab-store.s3.ap-south-1.amazonaws.com/repository/pages.zip
unzip pages.zip
git log --graph --decorate --oneline --all
git remote add origin https://RupchandDewangan@dev.azure.com/RupchandDewangan/pages-persistence-8/_git/pages-persistence-8
Verify remote -v 
git push origin master --tags

-------------------------------------------------------------------------------------------------------------------------------
Start --

git checkout -b pipeline pipeline-v1

./gradlew clean build

git push origin pipeline:master -f
----------------------------------------------------------- LAB 8 START

Follow steps from 1 - 6 , Create new file and Delete entire content using vi editor then gg  then dG.. Then past your content cntrl+shift+Insert

1. Create new Java class Page.java under org.dell.kube.pages

Add the following line in test.environment closure of build.gradle

2. Step 7, 8 and 9 - > Do carefully... 
mysql-pv.yaml
----------------

apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-volume-rupchand
  namespace: rupchand
  labels:
    type: local
spec:
  storageClassName: mysql-rupchand
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/mnt/data-rupchand"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-volume-claim-rupchand
  namespace: rupchand
spec:
  storageClassName: mysql-rupchand
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
	  
----------------------	  
mysql-secret.yaml
---------------------

apiVersion: v1
data:
  mysql-pass: cGFzc3dvcmQ=
kind: Secret
metadata:
  name: mysql-secret
  namespace: rupchand

-------------------------  
pages-deployment.yaml
-------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: pages
  name: pages
  namespace: rupchand
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pages
  strategy: {}
  template:
    metadata:
      labels:
        app: pages
    spec:
      containers:
        - image: rupchand1006/pages:persist2
          name: pages
          ports:
            - containerPort: 8080
          env:
            - name: PAGE_CONTENT
              valueFrom:
                configMapKeyRef:
                  name: pages-config-map
                  key: PAGE_CONTENT

--------------------------------------------------------
3. Compile the project

  Compilation issue /home/k8sworkshop/persistence-8/pages/src/main/java/org/dell/kube/pages/PageApplication.java:15: error: cannot find symbol
        public IPageRepository iPageRepository(DataSource dataSource){
                                               ^
    Add missing import-- import javax.sql.DataSource;

4.  MySql Server instance should be up and running 

	MYSQL Installation - 

	https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04

	sudo apt update

	sudo apt install mysql-server

	sudo mysql

	
	mysql> SELECT user,authentication_string,plugin,host FROM mysql.user;
	
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| user             | authentication_string                                                  | plugin                | host      |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| debian-sys-maint | $A$005$~S.NHCUA(ww+Z&1BVc.JveY7JhWuB9AxT/kCdoMW.7dIkBWsO3V2XwEC8 | caching_sha2_password | localhost |
| mysql.infoschema | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.session    | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.sys        | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| root             |                                                                        | auth_socket           | localhost |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
5 rows in set (0.00 sec)


5. Update connection without password	

    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'insert_password';
	
	Without password
	
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '';
	
6. To see the Mysql status - > systemctl status mysql.service 

	k8sworkshop@ML-RefVm-848478:~/persistence-8/pages$ systemctl status mysql.service
	● mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2020-11-17 20:56:30 UTC; 4min 4s ago
   Main PID: 9431 (mysqld)
     Status: "Server is operational"
      Tasks: 38 (limit: 8335)
     Memory: 343.9M
     CGroup: /system.slice/mysql.service
             └─9431 /usr/sbin/mysqld
	
7. Skip the test cases and run with -x test

	./gradlew clean build -x test
	
8. After application started, verify running below command 

 > sudo mysql

 > mysql> show databases [Before application run, no pgaes database]
		-> ;
	+--------------------+
	| Database           |
	+--------------------+
	| information_schema |
	| mysql              |
	| performance_schema |
	| sys                |
	+--------------------+
	4 rows in set (0.00 sec)

	After applicatio run 

	mysql> show databases
		-> ;
	+--------------------+
	| Database           |
	+--------------------+
	| information_schema |
	| mysql              |
	| pages              |
	| performance_schema |
	| sys                |
	+--------------------+
	5 rows in set (0.00 sec)

9. mysql> use pages;

	Database changed
	mysql> show tables
		-> ;
	+-----------------+
	| Tables_in_pages |
	+-----------------+
	| pages           |
	+-----------------+
	1 row in set (0.00 sec)
	
	
	
10. Request the endpoint to create some records in Pages table CRUD operation - 
	
	Create a page
	curl -i -XPOST -H"Content-Type: application/json" localhost:8080/pages -d"{\"businessName\": \"Uber2\", \"address\": \"SanFrancisco, CA, USA\", \"categoryId\": 123, \"contactNumber\": \"0045987869\"}"

	Get a page by ID
	curl -i localhost:8080/pages/${PAGE_ID}

	Update a page by ID
	curl -i -XPUT -H"Content-Type: application/json" localhost:8080/pages/${PAGE_ID} -d"{\"businessName\": \"Uber Technologies\", \"address\": \"SanFrancisco, CA, USA\", \"categoryId\": 123, \"contactNumber\": \"0045987869\"}"


	Delete a page by ID
	curl -i -XDELETE -H"Content-Type: application/json" localhost:8080/pages/${PAGE_ID}

11. Again follow the same steps from 8 - 9 then use select query

mysql> select * from pages;
+------+-------------------+-----------------------+-------------+----------------+
| id   | business_name     | address               | category_id | contact_number |
+------+-------------------+-----------------------+-------------+----------------+
|  321 | XYZ               | Bangalore             |         123 | 1234567890     |
| 1002 | XYZ               | Bangalore             |         123 | 1234567890     |
| 1003 | XYZ               | Bangalore             |         123 | 1234567890     |
| 1004 | XYZ               | Bangalore             |         123 | 1234567890     |
| 1006 | XYZ               | Bangalore             |         123 | 1234567890     |
| 1007 | Uber Technologies | SanFrancisco, CA, USA |         123 | 0045987869     |
+------+-------------------+-----------------------+-------------+----------------+
6 rows in set (0.00 sec)



12. Now with AKS setup 
-----------------------------

follow 13-16 steps

17. is not required

18. Created seperate document to create pipeline usig starter, Gradle -> Maven - > Kubectl  [Agent or Starter Pipeline]

azure-pipelines.yml

----------------------------

# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: Gradle@2
  inputs:
    gradleWrapperFile: 'gradlew'
    tasks: 'build -x test'
    publishJUnitResults: true
    testResultsFiles: '**/TEST-*.xml'
    javaHomeOption: 'JDKVersion'
    jdkArchitectureOption: 'x64'
    jdkVersionOption: '1.11'
    sonarQubeRunAnalysis: false
- task: Docker@2
  inputs:
    containerRegistry: 'dockerRegistryServiceConnection'
    repository: 'rupchand1006/pages'
    command: 'buildAndPush'
    Dockerfile: '**/Dockerfile'
    tags: 'persist2'
- task: Kubernetes@1
  inputs:
    connectionType: 'Azure Resource Manager'
    azureSubscriptionEndpoint: 'MSDN Platforms Subscription(1)(79d8ffb4-b8c9-405e-acf9-aec0ebdc6f1e)'
    azureResourceGroup: 'myResourceGroup'
    kubernetesCluster: 'myAKSCluster'
    namespace: 'rupchand'
    command: 'apply'
    arguments: '-f /home/vsts/work/1/s/deployment/pages-namespace.yaml -f /home/vsts/work/1/s/deployment/mysql-pv.yaml -f /home/vsts/work/1/s/deployment/mysql-secret.yaml -f /home/vsts/work/1/s/deployment/mysql-deployment.yaml -f /home/vsts/work/1/s/deployment/pages-config.yaml -f /home/vsts/work/1/s/deployment/pages-service.yaml -f /home/vsts/work/1/s/deployment/pages-deployment.yaml'
    
-------------------------------------------------------------

$(Build.ArtifactStagingDirectory)/ == /home/vsts/work/1/s/, in my case it was /home/vsts/work/1/a/, thats reason it was failing.

19. Run pipeline, you application might have deployed into AKS, access it your system or VM but type has been set as "LoadBalancer"

	Create AKS configuration and enable HTTP routing like selected

	az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
	
    overwrite - yes
	
	kubectl config get-contexts
	
	kubectl config set-context --current --namespace=rupchand
	
20. Run few command
    kubectl get pods
	kubectl get service
	kubectl describe pod <pod name>
21. To get the service URL 
	 kubectl get service pages
	 
	
	NAME    TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)          AGE
	pages   LoadBalancer   10.0.48.39   20.193.144.203   8080:30896/TCP   8m37s
	
	k8sworkshop@ML-RefVm-848478:~$ curl http://20.193.144.203:8080
	Hello from page : Green-Pages coming from Yellow-World! k8sworkshop@ML-RefVm-848478:~$ curl http://20.193.144.203:8080/pages
	[]k8sworkshop@ML-RefVm-848478:~$ curl http://20.193.144.203:8080/pages/1
	
22. Use CURL command for CRUD operation 

	Create a page
	curl -i -XPOST -H"Content-Type: application/json" <EXTERNAL-IP>:8080/pages -d"{\"businessName\": \"Uber2\", \"address\": \"SanFrancisco, CA, USA\", \"categoryId\": 123, \"contactNumber\": \"0045987869\"}"

	Get a page by ID
	curl -i <EXTERNAL-IP>:8080/pages/${PAGE_ID}

	Update a page by ID
	curl -i -XPUT -H"Content-Type: application/json" <EXTERNAL-IP>:8080/pages/${PAGE_ID} -d"{\"businessName\": \"Uber Technologies\", \"address\": \"SanFrancisco, CA, USA\", \"categoryId\": 123, \"contactNumber\": \"0045987869\"}"


	Delete a page by ID
	curl -i -XDELETE -H"Content-Type: application/json" <EXTERNAL-IP>:8080/pages/${PAGE_ID}



22. kubectl run -it --rm --image=mysql:8.0 --restart=Never mysql-client -- mysql -h mysql -ppassword
    press enter 
	
		k8sworkshop@ML-RefVm-848478:~$ kubectl run -it --rm --image=mysql:8.0 --restart=Never mysql-client -- mysql -h mysql -ppassword
	If you don't see a command prompt, try pressing enter.

	mysql> show databases;
	+--------------------+
	| Database           |
	+--------------------+
	| information_schema |
	| mysql              |
	| pages              |
	| performance_schema |
	| sys                |
	+--------------------+
	5 rows in set (0.01 sec)

	mysql> use pages
	Reading table information for completion of table and column names
	You can turn off this feature to get a quicker startup with -A

	Database changed
	mysql> select * from pages;
	+----+---------------+-----------------------+-------------+----------------+
	| id | business_name | address               | category_id | contact_number |
	+----+---------------+-----------------------+-------------+----------------+
	|  1 | Uber2         | SanFrancisco, CA, USA |         123 | 0045987869     |
	|  2 | Uber3         | SanFrancisco, CA, USA |         123 | 0045987869     |
	+----+---------------+-----------------------+-------------+----------------+
	2 rows in set (0.00 sec)

	mysql>
mysql> logout

23. Delete AKS and template and server 

	
	






