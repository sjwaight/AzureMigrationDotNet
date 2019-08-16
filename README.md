# Migrating your apps to Azure

> Note: the original source of this project can be found in the [GitHub repository](https://github.com/microsoft/IgniteTheTour/tree/master/DEV%20-%20Building%20your%20Applications%20for%20the%20Cloud/DEV10/src/inventory-service) for Microsoft Ignite | The Tour 2018.

## .Net Core - Inventory Service

This project needs an instance of SQL Server, Azure SQL Database, or PostgreSQL.

The output from the project is a REST API that can be published on either Linux or Windows.

To build and run this solution requires [.Net Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## Local dev

If you wish to edit the project you must use Visual Studio ([Community Edition](https://visualstudio.microsoft.com/vs/community/) is fine for learning purposes). This will work on both Mac and PC.

In order to define the connection string to your database of choice you have two options:

1. Define it in your appsettings.json file (not recommended as it is insecure):

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "InventoryContext": "<sqldb-connection-string>"
  }
}
```

2. Create a user secret:

```
dotnet user-secrets set 'ConnectionStrings:InventoryContext' '<sqldb-connection-string>'
```

Run the application locally using `dotnet run` while in the InventoryService.Api folder.

## Running in the cloud or in containers

Set an environment variable named `ConnectionStrings__InventoryContext`.

(Or use Azure Key Vault (below))

#### Example Connection String

```
Server=tailwind32671.postgres.database.azure.com;Database=Tailwind;Port=5432;User Id=admin_1136@tailwind32671;Password={Your Password};SslMode=Require;"
```

- REST API docs can be accessed using Swagger UI: `/swagger`
- Get real-time inventory updates, see SignalR test page: `/www`

Optional: Use Azure SignalR Service by adding another secret (local dev only, use environment variable everywhere else):

```
dotnet user-secrets set 'SignalRServiceConnectionString' '<azure-signalr-connection-string>'
```

## Key Vault

Set 2 environment variables:
* `AzureServicesAuthConnectionString` = `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}`
* `KeyVaultEndpoint` = `https://{keyvaultname}.vault.azure.net/`


## Build the Docker Image

> Note: this is not required to deploy the application to a VM or Azure App Service.

If you have [Docker](https://docker.com) installed, run this command:

```console
docker build -t inventory-service .
```

You can swap out `inventory-service` for your own image name.

### Building with [ACR Builds](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-tutorial-quick-task)

Or you can use [Azure Container Registry Builds](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-tutorial-quick-task) to do it:

```console
az acr build -r my-registry -t inventory-service .
```
> Note: The `-r` argument has to be a registry you've created already.