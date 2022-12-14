> The content in this page is under construction and may change in the future.

The page describes the available actions that can be used in `app.yml` in Teams Toolkit.

# aadApp/create
This action will create a new AAD app for you if AAD_APP_CLIENT_ID environment variable is empty.

## Syntax:
```
  - uses: aadApp/create
    with:
      name: <your-application-name> # Required. The name of AAD application. Note: when you run configure/aadApp, the AAD app name will be updated based on the definition of manifest. If you don't want to change the name, ensure the name in AAD manifest is same with the name defined here.
      generateClientSecret: true # Required. If the value is false, the action will not generate client secret for you
```

## Output:
* AAD_APP_CLIENT_ID: the client id of AAD app
* AAD_APP_CLIENT_SECRET: the client secret of AAD app
* AAD_APP_OBJECT_ID: the object id of AAD app
* AAD_APP_TENANT_ID: the tenant id of AAD app
* AAD_APP_OAUTH_AUTHORITY_HOST: the host of OAUTH authority of AAD app
* AAD_APP_OAUTH_AUTHORITY: the OAUTH authority of AAD app

# aadApp/update
This action will update your AAD app based on give AAD app manifest. It will refer the `id` property in AAD app manifest to determine which AAD app to update.

## Syntax:
```
  - uses: aadApp/update
    with:
      manifestTemplatePath: ./aad.manifest.template.json # Required. Relative path to teamsfx folder. Environment variables in manifest will be replaced before apply to AAD app.
      outputFilePath : ./build/aad.manifest.${{TEAMSFX_ENV}}.json # Required. Relative path to teamsfx folder. This action will output the final AAD manifest used to update AAD app to this path.
```

## Output:
* AAD_APP_ACCESS_AS_USER_PERMISSION_ID: the id of access_as_user permission which is used to enable SSO

## Troubleshooting:
### Error message "Permission (scope or role) cannot be deleted or updated unless disabled first
This is a known issue that OAuth permission id for an existing permission in your AAD manifest is different than the id in AAD application. One possible reason is the value of `AAD_APP_ACCESS_AS_USER_PERMISSION_ID` environment variable in `.env.{env_name}` is out of sync.

To fix this error: find the id of `access_as_user` scope for your application in [AAD app registration portal](https://ms.portal.azure.com/#view/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) and set it to `AAD_APP_ACCESS_AS_USER_PERMISSION_ID` environment variable in `.env.{env_name}`.

![image](https://user-images.githubusercontent.com/16605901/204182487-8eb46f6d-cee6-4d97-9cd4-68db59d4a572.png)


# azureStorage/enableStaticWebsite
This action will enable static website setting in Azure Stroage.

## Syntax:
```
  - uses: azureStorage/enableStaticWebsite
    with:
      storageResourceId: ${{TAB_AZURE_STORAGE_RESOURCE_ID}}
      indexPage: index.html
      errorPage: error.html
```

## Output:
NA

# npx/command
This action will execute `npx` commands under specified directory with parameters. It can be used to run `gulp` commands to bundle and package sppkg.

## Syntax:
```
  - uses: npx/command
    with:
      workingDirectory: ./src
      args: gulp bundle --ship --no-color
  - uses: npx/command
    with:
      workingDirectory: ./src
      args: gulp package-solution --ship --no-color
```

## Output:
* A client-side solution package that is located in `{workingDirectory}`/sharepoint/solution/*.sppkg


# npm/command
This action will execute `npm` commands under specified directory with parameters. The parameter `workingDirectory` can be removed if you want to run this command in the project root.

## Syntax:
```
  - uses: npm/command
    with:
      args: run build
  - uses: npx/command
    with:
      workingDirectory: ./src
      args: install
```

## Output:
NA

# dotnet/command
This action will execute `dotnet` commands under specified directory with parameters. The parameter `workingDirectory` can be removed if you want to run this command in the project root.

## Syntax:
```
  - uses: dotnet/command
    with:
      args: publish
  - uses: dotnet/command
    with:
      workingDirectory: ./src
      args: install
```

## Output:
NA

# azureAppService/deploy
This action will upload and deploy the project to Azure APP Service. The parameter `workingDirectory` can be removed if you want to run this command in the project root.

## Syntax:
```
  - uses: azureAppService/deploy
    with:
      workingDirectory: ./src
      distributionPath: . # Deploy base folder
      ignoreFile: ./.webappignore # Can be changed to any ignore file location, leave blank will ignore nothing
      resourceId: ${{BOT_AZURE_APP_SERVICE_RESOURCE_ID}} # The resource id of the cloud resource to be deployed to
```

## Output:
NA

# azureFunctions/deploy
This action will upload and deploy the project to Azure Functions. The parameter `workingDirectory` can be removed if you want to run this command in the project root.

## Syntax:
```
  - uses: azureFunctions/deploy
    with:
      workingDirectory: ./src
      distributionPath: . # Deploy base folder
      ignoreFile: ./.webappignore # Can be changed to any ignore file location, leave blank will ignore nothing
      resourceId: ${{BOT_AZURE_APP_SERVICE_RESOURCE_ID}} # The resource id of the cloud resource to be deployed to
```

## Output:
NA

# azureStorage/deploy
This action will upload and deploy the project to Azure Storage. The parameter `workingDirectory` can be removed if you want to run this command in the project root.

## Syntax:
```
  - uses: azureStorage/deploy
    with:
      workingDirectory: ./src
      distributionPath: . # Deploy base folder
      ignoreFile: ./.webappignore # Can be changed to any ignore file location, leave blank will ignore nothing
      resourceId: ${{BOT_AZURE_APP_SERVICE_RESOURCE_ID}} # The resource id of the cloud resource to be deployed to
```

## Output:
NA


# spfx/deploy
This action will upload and deploy generated sppkg to SharePoint app catalog. You can create tenant app catalog manually or by setting `createAppCatalogIfNotExist` to true if you don't have one in current M365 tenant.

## Syntax:
```
  - uses: spfx/deploy
    with:
      createAppCatalogIfNotExist: false # If the value is true, this action will create tenant app catalog first if not exist, default value is `false`.
      packageSolutionPath: ./src/config/package-solution.json # Required. Path to package-solution.json in SPFx project. This action will honor the configuration to get target sppkg.
```

## Output:
NA

# teamsApp/copyAppPackageForSPFx
This action will copy the Teams App zipped package to `teams` folder in SPFx directory to keep it updated. This is to ensure user will have aligned experience whether to publish Teams App from Teams Toolkit or manually sync to Teams in SharePoint app catalog.

## Syntax:
```
  - uses: teamsApp/copyAppPackageForSPFx
    with:
      appPackagePath: ${{TEAMS_APP_PACKAGE_PATH}}
      spfxFolder: ./src # Path to SPFx solution.
```

## Output:
NA

# appsettings/generate
This action will override or add environment viriables to target file (e.g., appsettings.Development.json)

## Syntax:
```
  - uses: appsettings/generate
    with:
      target: ./appsettings.Development.json # Required. The relative path of project configuration file
      appsettings: # Required.
        BOT_ID: ${{BOT_ID}}
        BOT_PASSWORD: ${{SECRET_BOT_PASSWORD}}
```

## Output:
NA

# botFramework/createOrUpdateBot
This action will create or update the bot registration on [dev.botframework.com](https://dev.botframework.com/bots). If the bot registraion specified by `botId` does not exist, this action will create a new one; otherwise, this action will update it.

## Syntax:
```yml
  - uses: botFramework/createOrUpdateBot
    with: 
      botId: <your-microsoft-app-id> # Required. Microsoft App Id for the bot registration.
      name: <your-bot-name> # Required. The name of the bot registration.
      messagingEndpoint: <your-messaging-endpoint> # Required. The messaging endpoint of the bot registration.
      description: <your-description> # Optional. The description of the bot registration.
      iconUrl: <your-icon-url> # Optional. The icon url of the bot registration.
```

## Output:
NA

# env/generate
This action will generate environment variables to `.env` file.

## Syntax:
```yml
  - uses: env/generate
    with: 
      target: /path/to/your/.env/file # Optional. The path of a `.env` file. The default value is `./teamsfx/.env.${TEAMSFX_ENV}`.
      envs: 
        <your-env-key-1>: <your-env-value-1>
        <your-env-key-2>: <your-env-value-2>
```

## Output:
NA

# tools/install
This action will install the developing tools that are required to debug a Teams app.

## Syntax:
```yml
  - uses: tools/install
    with:
      devCert: # Optional. The SSL certificate for Teams Tab app. This action will generate a SSL certificate and install it to the system certificate management center.
        trust: true # Required. Whether to trust the SSL certificate.
      func: true # Optional. Azure Functions Core Tools.
      dotnet: true # Optional. .NET.
```

## Output
- SSL_CRT_FILE: The path of the certificate file of the SSL certificate.
- SSL_KEY_FILE: The path of the key file of the SSL certificate.
- FUNC_PATH: The path of the Azure Functions Core Tools binary.
- DOTNET_PATH: The path of the .NET binary.

# arm/deploy
This action will deploy given ARM templates parallelly

## Syntax
``` yaml
  - uses: arm/deploy
    with:
      subscriptionId: ${{AZURE_SUBSCRIPTION_ID}} # Required. You can use built-in environment variable `AZURE_SUBSCRIPTION_ID` here. TeamsFx will ask you select one subscription if its value is empty. You're free to reference other environment variable here, but TeamsFx will not ask you to select subscription if it's empty in this case.
      resourceGroupName: ${{AZURE_RESOURCE_GROUP_NAME}} # Required. You can use built-in environment variable `AZURE_RESOURCE_GROUP_NAME` here. TeamsFx will ask you to select or create one resource group if its value is empty. You're free to reference other environment variable here, but TeamsFx will not ask you to select or create resource group if it's empty in this case.
      templates:
      - path: ./infra/azure.bicep # Required. Relative path to teamsfx folder.
        parameters: ./infra/azure.parameters.json # Required. Relative path to teamsfx folder. TeamsFx will replace the environment variable reference with real value before deploy ARM template.
        deploymentName: your-deployment-name # Required. Name of the ARM template deployment.
      bicepCliVersion: v0.9.1 # Optional. Teams Toolkit will download this bicep CLI version from github for you, will use bicep CLI in PATH if you remove this config.
```

## Output
This action will covert ARM deployment output to environment variables, with following naming conversion rule for output names:

1. Alphabet characters will be converted to upper case
2. Non alphanumeric character will be converted to `_`
3. If output is a hierarchy object, elements in the hierarchy is separated by a double underscore `__`

Taking following bicep output as example
```
output endpoint string = 'example'
output all_resource_ids object = {
  azureWebApp: {
    apiResourceId: 'web app id 1'
    frontendResourceId: 'web app id 2'
  }
  azureStorageId: 'storage id'
}
```
They will be outputted as following environment variables
``` env
ENDPOINT=example
ALL_RESOURCE_IDS__AZUREWEBAPP__APIRESOURCEID=web app id 1
ALL_RESOURCE_IDS__AZUREWEBAPP__FRONTENDRESOURCEID=web app id 2
ALL_RESOURCE_IDS__AZURESTORAGEID=storage id
```