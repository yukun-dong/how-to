> The content is under construction and is subject to change in the future

# Add app setting to Azure services
If you use Azure to host your application, you can declare your app settings for your Azure services via ARM template. We suggest you go through this [document](https://docs.microsoft.com/en-us/microsoftteams/platform/toolkit/provision) to gain deeper understanding about the provision behavior of Teams Toolkit as well as how to customize the ARM template in the project.

## Add app settings to bot / messaging extension (hosted by Azure Web App)
You can follow these steps to add app settings to the Azure Web App that hosts your bot:
1. Open `{your_project_root}/.fx/configs/azure.parameters.{env}.json`
2. Add new properties to the value of provisionParameters, which will be available during provision. Here is an example:
    ```
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "provisionParameters": {
                "value": {
                    ...
                    "valueOfMyAppSettingToBeAdded": "actual app setting value"
                }
            }
        }
    }
    ```
    > Note: You can reference environment variable when setting the value. Visit this [document](https://docs.microsoft.com/en-us/microsoftteams/platform/toolkit/provision#referencing-environment-variables-in-parameter-files) for more details.
3. Open `{your_project_root}/templates/azure/provision/bot.bicep`
4. Find the `webApp` resource declaration in the file, the resource looks like this: `resource webApp 'Microsoft.Web/sites@2021-02-01'`
5. Declare your app settings in the app settings property, and set the values your defined in parameter file to them. Here is an example:
    ```
    resource webApp 'Microsoft.Web/sites@2021-02-01' = {
        kind: 'app'
        location: resourceGroup().location
        name: webAppName
        properties: {
            siteConfig: {
                appSettings: [
                    ...
                    {
                        name: 'MY_APP_SETTING_NAME'
                        value: provisionParameters.valueOfMyAppSettingToBeAdded // the value comes from parameter file
                    }
                ]
            }
        }
    }
    ```
6. Run `Teams: Provision in the cloud` command to apply your changes to Azure

## Add app settings to api (hosted by Azure Functions)
You can follow these steps to add app settings to the Azure Web App that hosts your bot:
1. Open `{your_project_root}/.fx/configs/azure.parameters.{env}.json`
2. Add new properties to the value of provisionParameters, which will be available during provision. Here is an example:
    ```
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "provisionParameters": {
                "value": {
                    ...
                    "valueOfMyAppSettingToBeAdded": "actual app setting value"
                }
            }
        }
    }
    ```
    > Note: You can reference environment variable when setting the value. Visit this [document](https://docs.microsoft.com/en-us/microsoftteams/platform/toolkit/provision#referencing-environment-variables-in-parameter-files) for more details.
3. Open `{your_project_root}/templates/azure/provision/function.bicep`
4. Find the `functionApp` resource declaration in the file, the resource looks like this: `resource functionApp 'Microsoft.Web/sites@2021-02-01'`
5. Declare your app settings in the app settings property, and set the values your defined in parameter file to them. Here is an example:
    ```
    resource functionApp 'Microsoft.Web/sites@2021-02-01' = {
        kind: 'functionapp'
        location: resourceGroup().location
        name: functionAppName
        properties: {
            siteConfig: {
                appSettings: [
                    ...
                    {
                        name: 'MY_APP_SETTING_NAME'
                        value: provisionParameters.valueOfMyAppSettingToBeAdded // the value comes from parameter file
                    }
                ]
            }
        }
    }
    ```
6. Run `Teams: Provision in the cloud` command to apply your changes to Azure