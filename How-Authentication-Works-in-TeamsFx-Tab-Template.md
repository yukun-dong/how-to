>Note
* This documentation applies to Teams Toolkit version >= 3.3.0, TeamsFx CLI version >= 0.11.0 and TeamsFx SDK version >= 0.5.0.
* If you are facing errors with old project templates created by Teams Toolkit or older TeamsFx SDK version, please go to the [Migrate](#migrate) or [FAQ](#faq) for solutions.

## Overview

A typical Teams tab application usually needs to obtain a currently logged-in user identity to build a single sign-on experience for the application user. To access user information protected by Azure Active Directory and to access data from services like Facebook and Twitter, the application needs to establish a trusted connection with those providers. For example, if your application is calling Microsoft Graph APIs to obtain a user's profile photo, you need to authenticate the user to retrieve the appropriate authentication tokens.

Microsoft Teams provides a general [authentication flow](https://docs.microsoft.com/en-us/microsoftteams/platform/tabs/how-to/authentication/auth-flow-tab) for Teams Tab applications to log in users. The sequence chart below shows how the authentication flow works in a Teams Tab app.

![sequence chart](https://docs.microsoft.com/en-us/microsoftteams/platform/assets/images/authentication/tab_auth_sequence_diagram.png)


## How authentication flow works in Teams Tab application

In the Tab application template created by Teams Toolkit, we leverage the [AAD Auth Code Flow](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow) as the authentication mechanism to provide user login experience in Teams Tab apps. The template provides a simple Teams Tab that can get user login information.

Latest TeamsFx SDK uses [msal-browser](https://www.npmjs.com/package/@azure/msal-browser) for Auth Code Flow with PKCE authentication. 

### The Login Flow

1. In the Teams Tab app, add the following code to trigger the login flow: `await credential.login(scope);`
> Note
* You need to add the following code to create a new TeamsUserCredential instance: `const credential = new TeamsUserCredential();`
* Due to the [limitation](https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-js-initializing-client-applications#single-instance-and-configuration) of msal, multiple instances of `TeamsUserCredential` is not recommended.

2. TeamsFx SDK will pop up a window and navigate to auth-start.html under `tabs/public` in your project. 
TeamsFx SDK will by default read `REACT_APP_START_LOGIN_PAGE_URL` from env as the endpoint of auth-start.html. You can customize your endpoint by updating `initiateLoginEndpoint` under `authentication` in `loadConfiguration()` in [useTeamsFx.js](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/js/default/src/components/sample/lib/useTeamsFx.js) or [useTeamsFx.ts](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/ts/default/src/components/sample/lib/useTeamsFx.ts). 
For example:
      ```
      loadConfiguration({
        authentication: {
          initiateLoginEndpoint: your-auth-start-page,
          clientId: clientId,
        },
        ...
      });
      ```
      
3. In auth-start.html, will navigate to AAD login page using [msal](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/login-user.md#login-the-user).
You can find the template of auth-start.html [here](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/js/default/public/auth-start.html) for JavaScript and [here](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/ts/default/public/auth-start.html) for TypeScript.

4. After login in the AAD login page, user will be redirected to auth-end.html. In auth-end.html, will use [msal](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/initialization.md#redirect-apis) to use Auth Code Flow with PKCE. Since msal use the session storage for [cache](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/caching.md), will return session storage to SDK. The scheme of response will be:
      ```
      {
        "sessionStorage": {
          "key": "value",
          ...
        },
        ...
      }
      ``` 
You can find the template of auth-end.html [here](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/js/default/public/auth-end.html) for JavaScript and [here](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/ts/default/public/auth-end.html) for TypeScript.

5. TeamsFx SDK will get the response from the auth-end page, and will:
      * If the response is empty or can not parse to JSON object, will return error.
      * If "code" exists in the response, will return error. You can check [here](#how-to-solve-the-error-found-auth-code-in-response-auth-code-is-not-support-for-current-version-of-sdk) for detail.
      * If "sessionStorage" exists in the response, will set the key value pairs to the session storage.
After this, the login flow has been completed. You can call the following command to get the graph client: `const graph = createMicrosoftGraphClient(credential, scope);`

### Get Token Flow
When getting token flow is triggered, Teams SDK will first try to use msal to check whether Access Token exists in session storage. Here msal will handle the scenario that the token exists and is expired. If succeeded, return Access Token. If failed to get Access Token from session storage, Teams SDK will try to use msal to launch silent SSO. If succeeded, return Access Token. If failed in silent SSO, you need to trigger the login flow to get Access Token.

## How to migrate from SDK version earlier than 0.5.0 to latest SDK

If you are working on an existing project and want to use the latest SDK, please follow the steps below to migrate your project.

*Existing projects are supported by New Teams Toolkit and TeamsFx CLI.*

1. Install the latest Teams Toolkit or TeamsFx CLI.

2. Remove Simple Auth Plugin from Active Plugins
Open `.fx/projectSettings.json`, remove `fx-resource-simple-auth` from `activeResourcePlugins` under `solutionSettings`.

3. Update SDK version.
Open a terminal, navigate to `/tabs` and execute: `npm install @microsoft/teamsfx@latest`

4. Update auth-start.html and auth-end.html.
    * Locate the new OAuth prompt page:
      * For JavaScript: [auth-start.html](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/js/default/public/auth-start.html) and [auth-end.html](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/js/default/public/auth-end.html).
      * For TypeScript: [auth-start.html](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/ts/default/public/auth-start.html) and [auth-end.html](https://github.com/OfficeDev/TeamsFx/blob/main/templates/tab/ts/default/public/auth-end.html).
    * Open `tabs/public`, replace current `auth-start.html` and `auth-end.html` with the new OAuth prompt page.

5. Update `loadConfiguration()` method.
    * Open the file with `loadConfiguration()` method. The default path is:
      * For JavaScript: `tab/src/component/sample/lib/useTeamsFx.js`
      * For TypeScript: `tab/src/component/sample/lib/useTeamsFx.ts`
    * Remove the following line:
      ```
      simpleAuthEndpoint: process.env.REACT_APP_TEAMSFX_ENDPOINT,
      ```

6. Remove Simple Auth bicep files and resources
    * Remove `templates/azure/provision/simpleAuth.bicep`
    * Remove `templates/azure/teamsFx/simpleAuth.bicep`
    * Remove the following lines from `templates/azure/provision.bicep`:
        ```
        // Resources for Simple Auth
        module simpleAuthProvision './provision/simpleAuth.bicep' = {
          name: 'simpleAuthProvision'
          params: {
            provisionParameters: provisionParameters
            userAssignedIdentityId: userAssignedIdentityProvision.outputs.identityResourceId
          }
        }

        output simpleAuthOutput object = {
          teamsFxPluginId: 'fx-resource-simple-auth'
          endpoint: simpleAuthProvision.outputs.endpoint
          webAppResourceId: simpleAuthProvision.outputs.webAppResourceId
        }
        ```
    * Remove the following lines from `templates/azure/config.bicep`:
        ```
        var simpleAuthCurrentAppSettings = list('${provisionOutputs.simpleAuthOutput.value.webAppResourceId}/config/appsettings', '2021-02-01').properties

        module teamsFxSimpleAuthConfig './teamsFx/simpleAuth.bicep' = {
          name: 'addTeamsFxSimpleAuthConfiguration'
          params: {
            provisionParameters: provisionParameters
            provisionOutputs: provisionOutputs
            currentAppSettings: simpleAuthCurrentAppSettings
          }
        }
        ```
    * If the project has been provisioned, please follow the steps to remove Simple Auth Service for all environments:
      * Open `.fx/states.${environment name}.json`, note the value of `resourceGroupName` which is the resource group name.
      * Open Azure Portal and find the resource group according to the resource group name noted in the previous step.
      * Delete the App Service and App Service Plan with suffix `simpleAuth`.
        
        *Note: If you have customized the name of your app service or app service plan, please delete the corresponding resource.*

7. Remove `teamsfx: auth start` task from `task.json`
    * Open `.vscode/task.json` and remove the following line:
      ```
      "teamsfx: auth start"
      ```

8.  To deploy your changes to Azure, you need to provision and deploy your project. For local debug, you can run local debug directly. After that, the app should work using Auth Code Flow with PKCE.
During provision or local debug, Teams Toolkit or TeamsFx CLI will add a new type "Single Page Application" to the Redirect URI of your Azure AD app. The Redirect URI of the type will be:
    ```
    ${frontendEndpoint}/blank-auth-end.html
    ${frontendEndpoint}/auth-end.html?clientId=${clientId}
    ```

## FAQ

#### How to solve the error: "Found auth code in response. Auth code is not supported for the current version of SDK.".
This error may occur when you are working with a project created using Teams Toolkit 3.3.0 or later on a project that does not use Auth Code Flow with PKCE. Perhaps the project is created by an earlier version of Teams Toolkit. Please refer to [Migrate](#migrate) for how to update your project, or you can simply downgrade your SDK version to `^0.4.0`.

#### How to solve the error: "If you see "AADSTS50011: The reply URL specified in the request does not match the reply URLs configured for the application", in the popup window, you may be using an unmatched version for TeamsFx SDK (version >= 0.5.0) and Teams Toolkit (version < 3.3.0) or TeamsFx CLI (version < 0.11.0)."

This error may occur when you are using Teams Toolkit version earlier than 3.3.0 or TeamsFx CLI version earlier than 0.11.0 to provision a project created by Teams Toolkit 3.3.0 or later which is using SDK 0.5.0 or later. You need to upgrade your Teams Toolkit or TeamsFx CLI to the latest version. Provision the project again before you run the app.

#### How to solve the error: "simpleAuthEndpoint in configuration is invalid".
This error may occur when you are using the TeamsFx SDK version earlier than 0.5.0 on an up-to-date project created by Teams Toolkit version 3.3.0 or later. You need to upgrade the SDK version to >= 0.5.0.