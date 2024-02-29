<h1 align="center"> <strong>SECURING AND ORCHESTRATING OAUTH2 WEB APPLICATION APIs USING SPRING SECURITY AND AZURE SERVICES</strong> </h1>
<h3 align="center"> <strong>:point_right: This is my project for the network security course at university (NT140) :point_left:</strong> </h3>
<p>This report will demonstrate how Spring Security can effectively prevent common security risks in web applications such as authentication-based attacks, authorization errors, CORS attacks, and DDoS. Furthermore, I will provide a detailed explanation of how Azure services can be utilized to deploy and orchestrate the web application.</p>

#### An actual version of frontend build deployed to Vercel and backend deployed to Azure Kubernetes Service:
https://atmproject.vercel.app <br>

## Overview:
<h3 align="center"> <strong>Sign up page</strong> </h3>

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/e9954bf3-3f93-4a36-9e9e-8bf23bddaa9d)

<h3 align="center"> <strong>Login page</strong> </h3>

![image](https://github.com/Namtayto/spring-boot-oauth2-aks/assets/98264996/40be4f13-c0c3-48a9-97be-3c164d595060)

## Used Technologies:

* **Back-end:** Spring (Boot, Data, Security), JPA / Hibernate, PostgreSQL, Bucket4J
* **Front-end:** React.js
* **Security:** Oauth2, Rate Limiting
* **Azure:** AKS, ACR, AML, Azure Monitor, Application Insights, Grafana, Prometheus
* **Deploy:** Vercel, AKS
  
## Features:
* Regular Username/Password authentication.
* Sign in using either your Google or GitHub account.
* Monitoring and tracking APIs
