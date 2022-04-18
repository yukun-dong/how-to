> The content is under construction and is subject to change in the future
> This section describes the built-in app setting support by Teams Toolkit. You're free to use other approaches to configure your app settings.

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

# Add app setting for local debugging

Each project (tab / bot / api) has `.env.teamsfx.local` file in the root that defines environment variables available during local debugging. You can follow the syntax of [dotenv](https://www.npmjs.com/package/dotenv) package to add customized environment variables to it. If `.env.teamsfx.local` file does not exist, you can create an empty file and modify it.

For example, to add `CUSTOM_APP_SETTING` to your bot project, you can edit `bot/.env.teamsfx.local` file and add following content to it:
```
CUSTOM_APP_SETTING=my_app_setting_value
```

# App settings predefined by Teams Toolkit

Teams toolkit will define necessary app settings for your Teams app. You can refer following table to learn where to find the predefined app settings:
| Project | Predefined app settings for Azure (via [bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/)) | Predefined app settings for local debugging (via [dotenv](https://www.npmjs.com/package/dotenv)) |
| --- | --- | --- |
| tab | N/A | tabs/.env.teamsfx.local |
| bot | templates/azure/teamsfx/bot.bicep | bot/.env.teamsfx.local |
| api | templates/azure/teamsfx/function.bicep | api/.env.teamsfx.local |

> Note: the .env.teamsfx.local is generated during local debugging. You need to local debug first to see this file.