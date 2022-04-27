# Embed existing web page
> Please be advised these features are currently under active development, with a lot of changes taking place. Please expect breaking changes as we continue to iterate.
We really appreciate your feedback! If you encounter any issue or error, please report issues to us [here](https://github.com/OfficeDev/TeamsFx/issues/new/choose).

> How to enable preview features
> 1. Upgrade to the latest [Teams Toolkit](https://marketplace.visualstudio.com/items?itemName=TeamsDevApp.ms-teams-vscode-extension).
> 1. Open Visual Studio Code and find `Manage` icon from sidebar (Bottom Left) 
> 1. Select `Settings` and find `Teams Toolkit` under `Extensions` section.
> 1. Tick the checkbox for `Enable Preview Features`.
> 1. Restart Visual Studio Code.

Teams Toolkit helps you embed your running web pages as Teams tab application easily.

In this tutorial, you will learn:
* [What's the prerequisite to embed an existing web page in Teams](#Prerequisites)
* [How to embed your existing web pages by creating an empty project with Teams Toolkit](#Create-a-new-Existing-Tab-Project)
* [How to understand the empty project](#Take-a-tour-of-your-app-source-code)
* [How to preview your existing web pages in Teams](#Preview-your-Teams-app)


## Prerequisites
* An [M365 account for development](https://docs.microsoft.com/microsoftteams/platform/toolkit/accounts).

Also, make sure that your existing app adhere to the following prerequisites:
  * Allow your tab pages to be discovered in an iFrame, using X-Frame-Options and Content-Security-Policy HTTP response headers.
    * Set header: `Content-Security-Policy: frame-ancestors teams.microsoft.com *.teams.microsoft.com *.skype.com`
    * For Internet Explorer 11 compatibility, set `X-Content-Security-Policy`.
    * Alternately, set header `X-Frame-Options: ALLOW-FROM https://teams.microsoft.com/`. This header is deprecated but still accepted by most browsers.
  * Browsers same-origin policy restriction prevents webpages from making requests to different domains than the served web page. So, you can redirect the configuration or content page to another domain or subdomain. Your cross-domain navigation logic needs to allow the Teams client to validate the origin against a static `validDomains` list in the app manifest when loading or communicating with the tab.
  * Microsoft Teams tab doesn't support the ability to load intranet websites that use self-signed certificates.

<p align="right"><a href="#Embed-existing-web-page">back to top</a></p>

## Create a new Existing Tab Project

### In Visual Studio Code

1. From Teams Toolkit sidebar click `Create a new Teams app` or select `Teams: Create a new Teams app` from command palette.

![image](https://user-images.githubusercontent.com/11220663/165435370-99aa79b8-044f-44ea-b2a9-e42a055a3f6c.png)

2. Select `Create a new Teams app`.

![image](https://user-images.githubusercontent.com/11220663/165435420-566f8b99-ab44-482e-ba5f-857f80af4081.png)
   
3. Select the `Embed existing web app` from Scenario-based Teams app section.

![image](https://user-images.githubusercontent.com/11220663/165440419-01df1c66-ce23-4c16-a957-d98fff9582f9.png)
  
4. Enter your web app's local endpoint and press enter. Please make sure it's on `HTTPS`

![image](https://user-images.githubusercontent.com/11220663/165440497-6211d374-366b-4c91-8978-d2d06ef9746a.png)

5. Enter an application name and then press enter.

![image](https://user-images.githubusercontent.com/11220663/165435852-686deaef-119e-4311-9343-d8ef4b335516.png)
   
### In TeamsFx CLI

* If you prefer interactive mode, execute `teamsfx new` command, then use the keyboard to go through the same flow as in Visual Studio Code.

* If you prefer non-interactive mode, enter all required parameters in one command.

`teamsfx new --interactive false --capabilities "existing-tab" --existing-tab-endpoint "https://localhost:3000" --folder "./" --app-name myAppName`

<p align="right"><a href="#Embed-existing-web-page">back to top</a></p>

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

<p align="right"><a href="#Embed-existing-web-page">back to top</a></p>

## Preview your Teams app

* For local environment: you could preview the Teams app directly via environment tree view in Teams Toolkit:

![image](https://user-images.githubusercontent.com/11220663/165440914-f002a0ac-67d9-40dd-977d-247ab9d3dd2e.png)

* For remote environment: you need to run `Teams: Provision in the cloud` first, then you could preview the Teams app via environment tree view in Teams Toolkit:

![image](https://user-images.githubusercontent.com/11220663/165440981-b2b7a711-4ea8-4075-93dd-5ce94fd2ad5f.png)

<p align="right"><a href="#Embed-existing-web-page">back to top</a></p>


## Frequently Asked Questions

### Why the existing web app endpoint must be HTTPS secured?

Microsoft Teams is an entirely cloud-based product, it requires all services it accesses to be available publicly using HTTPS endpoints.


