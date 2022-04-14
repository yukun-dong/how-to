# Display Existing Web App in Teams

## Create a new Existing Tab Project
In Visual Studio Code:

1. Open Teams Toolkit Select the Teams Toolkit ![teams-toolkit-sidebar-icon](https://user-images.githubusercontent.com/10163840/160794831-e0a370ce-888f-4176-bb26-f16a64b72118.png) icon in the Visual Studio Code sidebar or select `Teams: Create a new Teams app` from command palette.

1. Select `Create a new Teams app`.
    
   <img src="https://user-images.githubusercontent.com/10163840/160793793-630fe4dd-ff92-4d43-8bf4-47c12a10e0b5.png" width=500>
   
1. Select the `Existing Tab` capability.
   
   <img src="https://user-images.githubusercontent.com/15644078/161890885-7ef47aac-5db1-4f55-94ce-f980e6380f06.png" width=500>
  
1. Provide your existing web app endpoint and click `OK`.
    
   <img src="https://user-images.githubusercontent.com/15644078/161890945-e2f4b95f-8d0b-408d-9884-f5922c5b324d.png" width=500>
  
1. Provide your app name and click `OK`.
    
   <img src="https://user-images.githubusercontent.com/10163840/160793811-014c9b88-4fc8-4785-bb51-a861f7433c91.png" width=500>
   
In CLI, use the `teamsfx new` command: 

- By default, `teamsfx new` goes into interactive mode and guides you through the process of creating a new Teams application.
- Or, if you prefer non-interactive mode, enter all required parameters in one command.
    ```
    teamsfx new --interactive false --capabilities "existing-tab" --existing-tab-endpoint "https://localhost:3000" --folder "./" --app-name myAppName
    ```
## Take a tour of your app source code

The following table lists all the scaffolded folder and files by Teams Toolkit:

| File name | Contents |
|- | -|
|`.fx/configs/config.local.json`| Configuration file for local environment |
|`.fx/configs/config.dev.json`| Configuration file for dev environment |
|`.fx/configs/projectSettings.json`| Global project settings, which apply to all environments |
|`templates/appPackage/manifest.template.json`|Teams app manifest template|
|`templates/appPackage/resources`|Teams app's icon referenced by manifest template|
|`.gitignore` | The git ignore file to exclude local files from TeamsFx project |

## Prerequisites
* An [M365 account for development](https://docs.microsoft.com/microsoftteams/platform/toolkit/accounts).
* Ensure that your existing app adhere to the following prerequisites:
  * Allow your tab pages to be discovered in an iFrame, using X-Frame-Options and Content-Security-Policy HTTP response headers.
    * Set header: `Content-Security-Policy: frame-ancestors teams.microsoft.com *.teams.microsoft.com *.skype.com`
    * For Internet Explorer 11 compatibility, set `X-Content-Security-Policy`.
    * Alternately, set header `X-Frame-Options: ALLOW-FROM https://teams.microsoft.com/`. This header is deprecated but still accepted by most browsers.
  * Browsers same-origin policy restriction prevents webpages from making requests to different domains than the served web page. So, you can redirect the configuration or content page to another domain or subdomain. Your cross-domain navigation logic needs to allow the Teams client to validate the origin against a static `validDomains` list in the app manifest when loading or communicating with the tab.
  * Microsoft Teams tab doesn't support the ability to load intranet websites that use self-signed certificates.
* Launch your existing app and update the endpoint in the environment config file (`.fx/configs/config.[env].json`) accordingly.

## Frequently Asked Questions

### Why the existing web app endpoint must be HTTPS secured?

Microsoft Teams is an entirely cloud-based product, it requires all services it accesses to be available publicly using HTTPS endpoints.


