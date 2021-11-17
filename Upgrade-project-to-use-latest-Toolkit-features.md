Teams Toolkit continuously evolve to offer useful features for developers. Recently a group of new features are available to preview in insider features, such as provisioning Azure resources with ARM templates and managing multiple environments. These insider features will require an update to your existing project code structures. Teams Toolkit can automatically upgrade your original project to support Multiple Environments and ARM provision features.

> Please note that ARM and multiple environment features will be enabled by default in the future release of Teams Toolkit. We thrive to make Teams Toolkit compatible with existing projects but long time backward compatibility is not 100% guaranteed thus we strongly recommend you updating your project configurations to continue using the latest Teams Toolkit.

## How to upgrade
You will need to enable the insider features, and Teams Toolkit will automatically upgrade your original project folder structure. Learn [how to enable insider preview features](https://github.com/OfficeDev/TeamsFx/wiki/Enable-Preview-Features-in-Teams-Toolkit#how-to-enable-preview-features).

## File structure change
Once migration succeeds, your project file structure will be changed.
We will generate a new folder named `templates` under root path, and `configs`, `migrationbackup`, `publishProfiles` under `.fx` folder.

All other original files will be deleted except `subscriptioninfo.json` under `.fx` folder.

## Backup your project
We recommend you initialize your project with `git` or backup the project before migration to better tracking file changes. Teams Toolkit will automatically backup `.fx/env.default.json` file to `.fx/migrationbackup`.

## Required Steps After Migration
If you have already provisioned the bot service before the migration, and you want to continue to use the bot service after the migration, please provision again. We will create a new bot service for this project, and other resources will not change.

## Manual work to use existing bot
There you need to modify three files
1. Modify `./templates/azure/provision/bot.bicep` with following config  
    ```bicep
    resource botService 'Microsoft.BotService/botServices@2021-03-01' = {
      kind: 'bot'
      location: 'global'
      name: botServiceName
      properties: {
        displayName: botDisplayName
        endpoint: uri('https://${webApp.properties.defaultHostName}', '/api/messages')
        msaAppId: botAadAppClientId
      }
      sku: {
        name: 'F0'
      }
    }
    ```
2. Add four parameter to `.fx/configs/azure.parameter.dev.json`, and replace the placeholder with the target value in `.backup/.fx/env.default.json`.
    ```json
    "botServiceName": "${fx-resource-bot.botChannelReg}",
    "botDisplayName": "${fx-resource-bot.botChannelReg}",
    "botServerfarmsName": "${fx-resource-bot.appServicePlan}",
    "botSitesName": "${fx-resource-bot.siteName}"
    ```
3. In the `.fx/states/state.dev.json`,
you need to remove the existing fx-resource-bot object, and add following fx-resource-bot object, please replace the placeholder with the value in `.backup/.fx/env.default.json`
    ```json
    "fx-resource-bot": {
            "botId": "${fx-resource-bot.botId}",
            "botPassword": "{{fx-resource-bot.botPassword}}",
            "objectId": "${fx-resource-bot.objectId}",
            "skuName": "${fx-resource-bot.skuName}",
            "siteName": "${fx-resource-bot.siteName}",
            "validDomain": "${fx-resource-bot.validDomain}",
            "appServicePlanName": "${fx-resource-bot.appServicePlan}",
            "botChannelRegName": "${fx-resource-bot.botChannelReg}",
            "botWebAppResourceId": "/subscriptions/${solution.subscriptionId}/resourceGroups/${solution.resourceGroupName}/providers/Microsoft.Web/sites/${fx-resource-bot.siteName}",
            "siteEndpoint": "${fx-resource-bot.siteEndpoint}"
        }
    ```

## Manual work to Customize APIM
After upgrade project, APIM related services are defined in *./templates/azure/provision/apim.bicep* and *./templates/azure/teamsFx/apim.bicep* with parameters in *.fx/configs/azure.parameters.dev.json*.

1. SKU, publisher name and publisher email of APIM service might be updated. To customize them, update SKU in *./templates/azure/provision/apim.bicep* directly and add `apimPublisherEmail` and `apimPublisherName` as customized parameters in *./.fx/configs/azure.parameters.dev.json*.
    ```bicep
    // ./templates/azure/provision/apim.bicep
    resource apimService 'Microsoft.ApiManagement/service@2020-12-01' = {
      name: apimServiceName
      location: resourceGroup().location
      sku: {
        name: '$your-customized-sku-name'
        capacity: 0
      }
      properties: {
        publisherEmail: publisherEmail
        publisherName: publisherName
      }
    }
    ```
    ```json
    //./.fx/configs/azure.parameters.dev.json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "provisionParameters": {
                "value": {
                    ...
                    "apimPublisherEmail": "your-apim-publisher-email",
                    "apimPublisherName": "your-apim-publisher-name",
                }
            }
        }
    }
    ```
2. If you prefer using an existing APIM service and don't want toolkit to update it. You can update Bicep file *./templates/azure/provision/apim.bicep* to use existing APIM service:
    ```bicep
    resource apimService 'Microsoft.ApiManagement/service@2020-12-01' existing = {
      name: apimServiceName
    }
    ```
    Same for APIM product service:
    ```bicep
    resource apimServiceProduct 'Microsoft.ApiManagement/service/products@2020-12-01' existing = {
      parent: apimService
      name: productName
    }
    ```


## Known Issues
* Local Debug will create a new teams App added to the Teams Developer Portal after migration success. You can get the app id from `.fx/configs/localSettings.json` file.
* Once upgrade success, if you provision resource in a new group resource using a newly created environment, this operation will cause an error. For example, you create a new environment named as test, in order to provision successful, just delete all parameters which value has exact value in  `.fx/configs/azure.parameters.test.json` 

