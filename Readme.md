<h1 align="center"> <strong>SECURING AND ORCHESTRATING OAUTH2 WEB APPLICATION APIs USING SPRING SECURITY AND AZURE SERVICES</strong> </h1>
<h3 align="center"> <strong>:point_right: This is my project for the network security course at university (NT140) :point_left:</strong> </h3>
<p>This report will demonstrate how Spring Security can effectively prevent common security risks in web applications such as authentication-based attacks, authorization errors, CORS attacks, and DDoS. Furthermore, I will provide a detailed explanation of how Azure services can be utilized to deploy and orchestrate the web application.</p>

#### An actual version of frontend build deployed to Vercel and backend deployed to Azure Kubernetes Service:
https://atmproject.vercel.app <br>

## Overview:
<h3 align="center"> <strong>Sign up page</strong> </h3>

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/e9954bf3-3f93-4a36-9e9e-8bf23bddaa9d)

<h3 align="center"> <strong>Sign in page</strong> </h3>

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/40be4f13-c0c3-48a9-97be-3c164d595060)

<h3 align="center"> <strong>After signing in the server fetches your mail, image, and name of your Google and GitHub account then shows to /profile:</strong> </h3>

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/8c92897e-bedf-41e0-905b-98903f8df14e)


## Used Technologies:

* **Back-end:** Spring (Boot, Data, Security), JPA / Hibernate, PostgreSQL, Bucket4J
* **Front-end:** React.js
* **Security:** Oauth2, Rate Limiting
* **Azure:** AKS, ACR, AML, Azure Monitor, Application Insights, Grafana, Prometheus
* **Deploy:** Vercel, AKS
* DNS: dnsexit
  
## Features:
* Regular Username/Password authentication.
* Sign in using either your Google or GitHub account.
* Monitoring and tracking APIs
* Implement rate limiting for APIs

## Rate Limiting:
IP Address based rate limiting on rest APIs by using Token Bucket Algorithm:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/0aef88f7-5d75-41db-a0a8-5471c4f99fce)

Rate limiting typically involves tracking the IP addresses where requests originate and identifying the time lapsed between requests. IP addresses are the application's main way to identify who has made each request. When request quota is consumed, it will throw error with 429 code which is Too many requests. <br>
The token bucket algorithm enables a network to allow or deny requests based on current traffic. Each bucket holds a certain number of tokens that represent network requests (e.g., attempting to log into an account or sending a message). Whenever a user sends a request, another token gets added to the bucket.

**Test rate limit:**
<br>
Using Postman to spam request to server:
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/1a417e14-1c47-4d97-9333-148ae2bcc32a)

When request quota is consumed, it will throw error with 429 code which is Too many requests.
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/da5a1ca0-cacf-448d-a012-f73e961a4467)

## Azure Services:
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
+ Push the image of Web application on Azure Container Registry
``` md
docker build -t spring-social .
az acr login --name springimage
docker tag spring-social:latest springimage.azurecr.io/spring-social:latest
docker push springimage.azurecr.io/spring-project:latest
```
![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/8d53b211-0390-4730-89da-4cdb55b9b43a)

