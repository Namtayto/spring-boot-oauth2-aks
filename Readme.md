<h1 align="center"> <strong>SECURING AND ORCHESTRATING OAUTH2 WEB APPLICATION APIs USING SPRING SECURITY AND AZURE SERVICES</strong> </h1>
<h3 align="center"> <strong>:point_right: This is my project for the network security course at university (NT140) :point_left:</strong> </h3>
<p>This report will demonstrate how Spring Security can effectively prevent common security risks in web applications such as authentication-based attacks, authorization errors, CORS attacks, and DDoS. Furthermore, I will provide a detailed explanation of how Azure services can be utilized to deploy and orchestrate the web application.</p>

#### An actual version of frontend build deployed to Vercel and backend deployed to Azure Kubernetes Service:
https://atmproject.vercel.app <br>

## Overview
<h3 align="center"> <strong>Sign up page</strong> </h3>

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/e9954bf3-3f93-4a36-9e9e-8bf23bddaa9d)

<h3 align="center"> <strong>Sign in page</strong> </h3>

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/40be4f13-c0c3-48a9-97be-3c164d595060)

<h3 align="center"> <strong>After signing in the server fetches your mail, image, and name of your Google and GitHub account then shows to /profile:</strong> </h3>

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/8c92897e-bedf-41e0-905b-98903f8df14e)


## Used Technologies

* **Back-end:** Spring (Boot, Data, Security), JPA / Hibernate, PostgreSQL, Bucket4J
* **Front-end:** React.js
* **Security:** Oauth2, Rate Limiting
* **Azure:** AKS, ACR, AML, Azure Monitor, Application Insights, Grafana, Prometheus
* **Deploy:** Vercel, AKS
* DNS: dnsexit
  
## Features
* Regular Username/Password authentication.
* Sign in using either your Google or GitHub account.
* Monitoring and tracking APIs
* Implement rate limiting for APIs

## Rate Limiting
IP Address-based rate limiting on rest APIs by using Token Bucket Algorithm:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/0aef88f7-5d75-41db-a0a8-5471c4f99fce)

Rate limiting typically involves tracking the IP addresses where requests originate and identifying the time lapsed between requests. IP addresses are the application's main way to identify who has made each request. When the request quota is consumed, it will throw an error with 429 code which is Too many requests. <br>
The token bucket algorithm enables a network to allow or deny requests based on current traffic. Each bucket holds a certain number of tokens that represent network requests (e.g., attempting to log into an account or sending a message). Whenever a user sends a request, another token gets added to the bucket.

**Test rate limit:**
<br>
Using Postman to spam requests to the server:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/1a417e14-1c47-4d97-9333-148ae2bcc32a)

When the request quota is consumed, it will throw an error with 429 code which is Too many requests.
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/da5a1ca0-cacf-448d-a012-f73e961a4467)

## Azure Services
### Azure Container Registry:
+ Write the Dockerfile:
```md
FROM maven:3.8.5-openjdk-17 AS build
COPY . .
RUN mvn clean package -Pprod -DskipTests

FROM openjdk:17.0.1-jdk-slim
EXPOSE 8080
COPY applicationinsights-agent-3.4.19.jar /
COPY applicationinsights.json /
COPY --from=build /target/spring-social-0.0.1-SNAPSHOT.jar spring-social.jar
ENTRYPOINT ["java", "-javaagent:/applicationinsights-agent-3.4.19.jar","-jar","spring-social.jar"]
```
+ Push the image of the Web application on Azure Container Registry
``` md
docker build -t spring-social .
az acr login --name springimage
docker tag spring-social:latest springimage.azurecr.io/spring-social:latest
docker push springimage.azurecr.io/spring-project:latest
```
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/8d53b211-0390-4730-89da-4cdb55b9b43a)

### Azure Kubernetes Service:
+ Expose the image as the service type load balancer in Azure Kubernetes:
```md
kubectl apply -f docker-k8s.yaml
kubectl get deployment
kubectl expose deployment docker-k8s-demo-deployment --port=80 --protocol=TCP --target-port=8080 --type=LoadBalancer
```
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/8143d5a9-a6b4-4ea2-931e-220e541c0497)

+ After exposing we have the public IP: 20.44.199.131. Point the free domain from dnsexit.com to that public IP:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/d0da1ba9-9def-4b08-9f65-3f66220ba0e7)

+ Create the SSL certificates (ZeroSSL) for that domain:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/06e64f6b-b268-40f7-8316-21c1f380a248)

+ Then we can use that domain instead of using public IP:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/dca0c0e9-7c3f-45e7-9f8c-faf274601aa1)

## Azure Monitor
### Monitoring AKS using Prometheus and Grafana
+ CPU:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/05b46bf9-c552-4d52-a4ff-f53c22513ca7)

+ Memory:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/a73304b2-6997-4fe1-a025-0d25d8f4d682)

+ Network:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/1ec45fe5-1546-439e-bb6e-f9cfa4b62620)

+ Storage:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/41018916-28a0-40b1-8e07-3eb9d3c4ecaf)

+ Containers:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/cbdb6f1b-af35-4ebd-b015-91e1a4357ce7)

### Alert
+ Set an alert and issue a warning if the RAM usage of the cluster exceeds 50%:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/1e4dffdf-333e-4ad7-8dd2-10c288b941c1)

+ Based on the Alert we can scale up or scale down the Virtual Machine to save money:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/279a6b63-4a63-4435-82b5-3b00ae00c631)

### Application Insight
+ On the map of our application, you can see the request being made to the cloud database node:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/97e222fd-582b-491d-af33-682749b270b7)

+ Trigger the email of users when they log in or signup:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/52d0b87d-f7bc-4f12-b991-7c98eed0642e)

## Azure Machine Learning
+ Retrieve the data monitor from Application Insights and export it as a CSV file to use as the dataset in Azure Machine Learning:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/19d4be0d-26da-42d2-a85b-cbd1e28400ae)

+ Using Jupyter Notebook connect to workspace has the dataset:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/aa511ca8-48ef-4b6b-97f0-701981df9796)

+ Read the dataset using pandas:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/39baa3d2-655f-4cbb-a7e4-985d1fc1f8da)

+ Draw graph:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/9fdee7b7-54d3-43a6-ac32-2d8d1aeafb76)

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/b9503877-86be-42d6-9727-8cdf6e590644)

+ Calculate Mean Squared Error and Coefficients of the model:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/90039e49-dbf7-49d0-a3dd-8a22167908af)



