# Embed Existing Web App
> Please be advised these features are currently under active development, with a lot of changes taking place. Please expect breaking changes as we continue to iterate.
We really appreciate your feedback! If you encounter any issue or error, please report issues to us [here](https://github.com/OfficeDev/TeamsFx/issues/new/choose).

## How to enable preview features
1. Upgrade to the latest [Teams Toolkit](https://marketplace.visualstudio.com/items?itemName=TeamsDevApp.ms-teams-vscode-extension).
1. Open Visual Studio Code and find `Manage` icon from sidebar (Bottom Left) 
1. Select `Settings` and find `Teams Toolkit` under `Extensions` section.
1. Tick the checkbox for `Enable GA Preview Features.
1. Restart Visual Studio Code.

## Prerequisites
* An [M365 account for development](https://docs.microsoft.com/microsoftteams/platform/toolkit/accounts).

Also, make sure that your existing app adhere to the following prerequisites:
  * Allow your tab pages to be discovered in an iFrame, using X-Frame-Options and Content-Security-Policy HTTP response headers.
    * Set header: `Content-Security-Policy: frame-ancestors teams.microsoft.com *.teams.microsoft.com *.skype.com`
    * For Internet Explorer 11 compatibility, set `X-Content-Security-Policy`.
    * Alternately, set header `X-Frame-Options: ALLOW-FROM https://teams.microsoft.com/`. This header is deprecated but still accepted by most browsers.
  * Browsers same-origin policy restriction prevents webpages from making requests to different domains than the served web page. So, you can redirect the configuration or content page to another domain or subdomain. Your cross-domain navigation logic needs to allow the Teams client to validate the origin against a static `validDomains` list in the app manifest when loading or communicating with the tab.
  * Microsoft Teams tab doesn't support the ability to load intranet websites that use self-signed certificates.

## Create a new Existing Tab Project
In Visual Studio Code:

1. Open Teams Toolkit Select the Teams Toolkit ![teams-toolkit-sidebar-icon](https://user-images.githubusercontent.com/10163840/160794831-e0a370ce-888f-4176-bb26-f16a64b72118.png) icon in the Visual Studio Code sidebar or select `Teams: Create a new Teams app` from command palette.

1. Select `Create a new Teams app`.

   <img src="https://user-images.githubusercontent.com/15644078/163351003-dd3eaa81-a66e-4d27-a4c2-aa6a196c3f12.png" width=500>
   
1. Select the `Embed existing web app` capability.

   <img src="https://user-images.githubusercontent.com/15644078/163351136-37abc835-8144-4db8-9653-d8389c98faa9.png" width=500>
  
1. Enter your web app's local endpoint and continue.

   <img src="https://user-images.githubusercontent.com/15644078/163351438-0ae226d4-d931-4aa4-88de-9c6daa9c20e8.png" width=500>
  
1. enter your app name and click `OK`.
    
   <img src="https://user-images.githubusercontent.com/15644078/163351597-e1f0652d-d9bd-46f6-bada-7d46b0a82312.png" width=500>
   
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

## Preview your Teams app

* For local environment: you could preview the Teams app directly via environment tree view in Teams Toolkit:

  <img src="https://user-images.githubusercontent.com/15644078/163303755-54d24c19-a020-473f-b1b4-c3ef454f09c5.png" width=300>

* For remote environment: you need to run `Teams: Provision in the cloud` first, then you could preview the Teams app via environment tree view in Teams Toolkit:

  <img src="https://user-images.githubusercontent.com/15644078/163303988-887b72f7-eff6-4746-96ac-51c82af27ead.png" width=300>

## Frequently Asked Questions

### Why the existing web app endpoint must be HTTPS secured?

Microsoft Teams is an entirely cloud-based product, it requires all services it accesses to be available publicly using HTTPS endpoints.


