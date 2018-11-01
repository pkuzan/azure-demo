# Pricer App Service 

This project demonstrates how to deploy a s Spring Boot application to Azure App Service using the 
azure-webapp-maven-plugin.

`Pricer` uses Spring Data JPA for persistence. This will be used with SQL Server.

The issue with this solution is vanilla Java applications are not directly supported by App Service.
App Service only officially supports the deployment of Java applications into containers such as Tomcat or Docker.
A Tomcat container is started by this project, but it is not used. 
A web.config file specifies the startup behavior. 

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
