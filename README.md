<p align="center">
  <img src="img/azure.png" width="200px"/>
  <br>
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

[For more information, click here](https://docs.microsoft.com/en-gb/azure/app-service/app-service-web-overview)

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

[For more information, click here](https://docs.microsoft.com/en-us/azure/app-service/environment/intro)
## Modifications 
### Code
As App Service supports Java directly (no container required), a Spring Boot application can be deployed with 
no code modifications.
### Configuration
The simplest way to deploy a Java application to App Service is to use the `azure-webapp-maven-plugin`.  
The following plugin needs to be added to `pom.xml`.
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
|Parameter | Value |
| --- | --- |
| deploymentType | jar or war |
| resourceGroup | Azure Resource Group to deploy to |
| appName | name of the application - must be unique |
| region | Azure region to deploy to |
| linuxRuntime | Runtime to use |

[For more information, click here](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)

### Authentication
The Azure maven plugin needs to authenticate with Azure, there are 2 ways to do this, via the CLI or using a Service Principal, 
for this demonstration, we'll authenticate via the CLI.

Instructions to install the Azure CLI can be found [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).  
Open a command or terminal and type `az login`, follow the instructions in the response.
```
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXXX to authenticate.
```
You are now authenticated.

### Deploy
The application needs to be built and then deployed. In a Terminal window type the following commands.
```
mvn package
mvn azure-webapp:deploy
```

