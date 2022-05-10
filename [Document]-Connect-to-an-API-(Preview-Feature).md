# Connect to existing APIs

> This feature is currently under active development. Report any issues to us [here](https://github.com/OfficeDev/TeamsFx/issues/new/choose).

> How to enable preview features
> 1. Upgrade to the latest [Teams Toolkit](https://marketplace.visualstudio.com/items?itemName=TeamsDevApp.ms-teams-vscode-extension).
> 1. Open Visual Studio Code and find `Manage` icon from sidebar (Bottom Left) 
> 1. Select `Settings` and find `Teams Toolkit` under `Extensions` section.
> 1. Tick the checkbox for `Enable Preview Features`.
> 1. Restart Visual Studio Code.

When building a Teams application often you will want to access existing APIs - these might be APIs developed by your organization or even 3rd party APIs. If you do not have language appropriate SDKs to access these APIs, Teams Toolkit helps you bootstrap sample code to access these APIs.

In this tutorial, you will:

* [Use Teams Toolkit to connect to an existing API](#How-to-use-this-feature)
* [Understand the changes to your project when you connect to an existing API](#How-this-feature-changes-your-project)
* [Invoke the API in the Teams Toolkit local environment](#Invoke-API-in-local-environment)
* [Add configuration to access the API when you deploy your application to Azure](#Invoke-the-API-in-remote-environment)
* [Use a custom authentication provider](#Custom-authentication-provider)
* [Obtain API permissions with Azure Active Directory protected API request](#Gain-API-permission-for-your-Teams-apps-AAD-app-registration)

## How to use this feature

When you use Teams Toolkit to connect to an existing API, Teams Toolkit will:

* Generate sample code under `./bot` or `./api` folder.
* Add a reference to the `@microsoft/teamsfx` package to `package.json`.
* Add application settings for your API in `.env.teamsfx.local`, which configures local debugging.

To add an API connection:

### In Visual Studio Code
1. Open a TeamsFx project, from the Teams Toolkit side bar select `Add features` or open command palette and select `Teams: Add features`

![image](https://user-images.githubusercontent.com/11220663/165349151-bf009c88-907a-4fd1-9d2d-cdb26e2a93cc.png)

2. Scroll down and select `API Connection`

![image](https://user-images.githubusercontent.com/11220663/165430594-3793de91-ac4b-4746-9810-e004aadea19b.png)


3. Input endpoint for your API. 
The endpoint should be a valid http(s) url. It will be added to your project's local app setting and used as base url when making requests. This means you don't need to input full api url for every request.

![image](https://user-images.githubusercontent.com/11220663/165430748-9c61a7a0-e51e-4642-8735-04affdc2bf74.png)


4. Choose which component needs to invoke the API.

![image](https://user-images.githubusercontent.com/11220663/165430964-b5273e72-ee76-41f5-b103-3dcc440e17f7.png)

5. Input alias for your API. The alias is used to generate app setting names for your API, which will be added to your project's local app setting.

![image](https://user-images.githubusercontent.com/11220663/165431010-28b22571-31a4-4fcc-9b90-e14626ef182c.png)


6. Select how you want to authenticate the API requests. We will generate appropriate sample code and add corresponding local app settings based on your selection.

![image](https://user-images.githubusercontent.com/11220663/165431048-66b522d3-c616-4b84-b7aa-41a14f92696a.png)

7. There will be some additional questions based on your selection of an authentication type. Provide information for each question to complete the flow.

### In TeamsFx CLI

The base command of this feature is `teamsfx add api-connection [authentication type]`. Here is the list of available authentication type and corresponding sample command. You can use `teamsfx add api-connection [authentication type] -h` to get help document.
| Authentication type | Sample command |
| --- | --- |
| Basic | teamsfx add api-connection basic --endpoint https://example.com --component bot --alias example --user-name exampleuser --interactive false |
| API Key | teamsfx add api-connection apikey --endpoint https://example.com --component bot --alias example --key-location header --key-name example-key-name --interactive false |
| AAD | teamsfx add api-connection aad --endpoint https://example.com --component bot --alias example --app-type custom --tenant-id your_tenant_id --app-id your_app_id --interactive false |
| Certificate | teamsfx add api-connection cert --endpoint https://example.com --component bot --alias example --interactive false |
| Custom | teamsfx add api-connection custom --endpoint https://example.com --component bot --alias example --interactive false |

<p align="right"><a href="#Connect-to-an-API">back to top</a></p>

## How this feature changes your project

After you successfully triggered the command, the tooling will make following changes to `bot` or `api` folder based on selected component(s):
1. Generate `{your_api_alias}.js/ts`. The file demostrates how to initialize an API client for your API with support of `@microsoft/teamsfx` package, as well as exported the API client to be consumed in other source code files.
2. Add `@microsoft/teamsfx` package to `package.json` if your project does not have this package. The package provides support for common API authentication methods.
4. Add several environment variables to `.env.teamsfx.local` to provide necessary information for selected authentication type. The generated code will read values from these environment variables. You can rename the environment variables and update generated code to use your favorite names.

<p align="right"><a href="#Connect-to-an-API">back to top</a></p>

## Invoke API in local environment

After the command updated your project, please follow the instructions displayed by the tooling to invoke your APIs. Generally, you need to do following things. You can visit the generated file to get more details for these action items.

### 1. Run npm install
You need to run `npm install` under `bot` or `api` folder to install added packages. This ensures you can have Intellisense support when coding.

### 2. Add your API credentials to local app settings
The command will not ask any credentials when generating sample code and adding local app settings. But it will leave some placeholders for you to fill your credentials. Please fill your API credentials to `.env.teamsfx.local` under `bot` or `api` folder to avoid authentication failures for your API requests.

### 3. Reference the generated API client
The sample code initializes an API client instance for you and exports it. So in your source code, you can import the API client instance and use it:
``` ts
import { yourApiClient } from '{relative path to the generated file}'
```

### 4. Make http(s) requests to target API (with axios)
The generated API client is an axios instance. [Axios](https://www.npmjs.com/package/axios) is a popular nodejs package that helps you make http(s) requests. You can visit axios [documentation](https://axios-http.com/docs/example) to learn how to make http(s) requests with it.

## Invoke the API in remote environment
Please add app settings for your hosting environments if you are ready to move your application to the cloud.

Teams Toolkit only sets up app settings for your local environment to help you debug your code. Before you deploy your code to your hosting environments, you need to add necessary app settings to your hosting environments. The bootstrapped sample code contains comments that tell you what app settings you need to configure.

If you're using Azure to host your application, you can visit this [documentation](https://aka.ms/teamsfx-add-appsettings) to learn how to add app settings.

<p align="right"><a href="#Connect-to-an-API">back to top</a></p>

## Advanced scenarios

### Custom authentication provider
Besides the authentication provider included in `@microsoft/teamsfx` package, you can also implement your customized authentication provider that implements `AuthProvider` interface and use it in `createApiClient(..)` function:
``` ts
import { AuthProvider } from '@microsoft/teamsfx'

class CustomAuthProvider implements AuthProvider {
    constructor() {
        // You can add necessary parameters for your customized logic in constructor
    }

    AddAuthenticationInfo: (config: AxiosRequestConfig) => Promise<AxiosRequestConfig> = async (
        config
    ) => {
        /*
        * The config parameter contains all the request information and can be updated to include extra authentication info.
        * Refer https://axios-http.com/docs/req_config for detailed document for the config object.
        * 
        * Add your customized logic that returns updated config
        */
    };
}
```

<p align="right"><a href="#Connect-to-an-API">back to top</a></p>

### Gain API permission for your Teams app's AAD app registration

When using AAD app to authenticate service to service requests, there're 2 common ways to configure the API permissions: using Access Control List (ACL) or using AAD application permission. How to gain permission for your target API depends on the actual implementation of the API server. Here're the common steps to gain permission for your Teams app's AAD app registration.

#### Steps to gain permission for APIs that use AAD application permission for access control
1. Open `templates/appPackage/aad.template.json`, add following content to `requiredResourceAccess` property:
   ```
    {
        "resourceAppId": "fill your target API's app id",
        "resourceAccess": [
            {
                "id": "fill the id of target API's application permission",
                "type": "Role"
            }
        ]
    }
   ```
2. Start local debug or provision an cloud environment for your project. This step created AAD app for your Teams app.
3. Go to `.fx/states/state.{env}.json`, record the value of `clientId` under `fx-resource-aad-app-for-teams` property.
4. Follow this [document](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/grant-admin-consent#grant-admin-consent-in-app-registrations) to gain admin consent for the required application permission. You need step 3's client id when following the document to find AAD app registration.

#### Steps to gain permission for APIs that use Access Control List (ACL)
1. Start local debug or provision an cloud environment for your project. This step created AAD app for your Teams app.
2. Go to `.fx/states/state.{env}.json`, record the value of `clientId` under `fx-resource-aad-app-for-teams` property.
3. Provide the client id to your API provider to configure permissions from server side.

<p align="right"><a href="#Connect-to-an-API">back to top</a></p>
