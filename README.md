<p align="center">
  <img src="img/azure.png" width="350px"/>
  <img src="img/spring-boot-logo.png" width="200px"/>
</p>

# Deploy a Spring Boot application to Azure App Service.


The `Pricer` application is a vanilla Spring Boot application that uses embedded Tomcat and builds to an 
executable jar file containing all dependencies. 

* Spring Data JPA for persistence
  * H2, MySQL, SQL Server
* Angular SPA UI
* RESTful endpoint
  * Spring Rest
* Swagger UI

## Azure App Service  

* Multiple Languages and Frameworks (Linux)
  * Java SE, Tomcat and WildFly
  * .NET Core
  * Ruby
  * Node.js
  * PHP
  * Python
  * Go
  * Docker
* Security and Compliance
  * ISO, SOC, PCI
  * Authenticate with Active Directory
* Global Scale and High Availability
  * Scale out and up automatically 

For more information, click 
[here](https://docs.microsoft.com/en-gb/azure/app-service/app-service-web-overview)

There are 2 classes of Azure PaaS, App Service and App Service Environments (ASE)

### ASE 
* Dedicated, isolated environments
  * Windows
  * Linux
  * Docker
  * Functions
* Very high scale
* Isolation and secure network access
  * VNET support is essential for on-prem, Express Route connectivity
   
![](img/networkase-overflow.png?raw=true)   

### Modifications 
## Code
As App Service supports Java directly (no container required), a Spring Boot application can be deployed with 
no code modifications.
## Configuration
The simplest way to deploy a Java application to App Service is to use the `azure-webapp-maven-plugin`. 

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.4.1</version>
    <configuration>
        <deploymentType>jar</deploymentType>

        <appSettings>
            <property>
                <name>JAVA_OPTS</name>
                <value>-Dserver.port=80</value>
            </property>
        </appSettings>

        <!-- Web App information -->
        <resourceGroup>HelloCloud</resourceGroup>
        <appName>pk-007-pricer</appName>
        <region>eastus</region>

        <!-- Java Runtime Stack for Web App on Linux-->
        <linuxRuntime>jre8</linuxRuntime>
    </configuration>
</plugin>
```

## Setup

The Azure maven plugin needs to authenticate with Azure, a Service Principal is used for this purpose. 
```
az login
az account show --query "{subscriptionId:id, tenantId:tenantId}"
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/YOUR_SUBSCRIPTION_ID;"
```

It will respond :
```
{
  "appId": "APP_ID",
  "displayName": "NAME",
  "name": "http://NAME",
  "password": "PASSWORD",
  "tenant": "TENNANT_ID"
}
```


In your Maven settings.xml add the following, replacing the placeholders with your values, this 
will be used to authenticate the plugin.

```
<servers> 
   <server>
     <id>azure-auth</id>
      <configuration>
         <client>APP_ID</client>
         <tenant>TENNANT_ID</tenant>
         <key>PASSWORD</key>
         <environment>AZURE</environment>
      </configuration>
   </server>
</servers> 
```
The appName and resourceGroup attributes of the azure-webapp-maven-plugin in the POM will need to be edited, 
appName needs to be something globally unique and resourceGroup should refer to an existing Resource Group otherwise a 
new one will be created. 

```
<resourceGroup>HelloCloud</resourceGroup>
<appName>hello-cloud-pk-007</appName>
```

Run azure-webapp:deploy to deploy to App Service.
