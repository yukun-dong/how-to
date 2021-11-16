> [!NOTE]
> Currently, this feature is available in [developer preview](https://docs.microsoft.com/en-us/microsoftteams/platform/resources/dev-preview/developer-preview-intro) only.

Teams Toolkit helps you to upgrade Teams applications to work with Outlook and Office.com. To extend your Teams applications in Outlook and Office.com, the migration commands in Teams are as follows:

1. Use the command **Teams: Upgrade Teams manifest to support Outlook and Office apps** to upgrade manifest to the latest version.

1. Use the command **Teams: Upgrade Teams JS SDK references to support Outlook and Office apps** to upgrade `TeamsJS` SDK to the latest version.

![teams-extended-in-outlook-and-office](https://user-images.githubusercontent.com/37978464/141744549-6cbec5fc-238e-4291-afa0-d3b526e9aba2.png)

> [!NOTE]
> To extend your Teams application in Outlook and Office.com, upgrading manifest file is required. However, it's optional for you to upgrade the `TeamsJS` SDK, as the old version continues to work.
> [!IMPORTANT]
> If you have both `TeamsFx` and `TeamsJS` SDK packages in your Teams application and you have upgraded `TeamsJS` SDK to `2.0.0-beta.1` or higher, you need to change `TeamsFx` version to `0.3.1-beta.0` or higher in your `package.json` file to avoid version conflicts.
## Prerequisites

1. Install `2.10.0` or later version of Teams Toolkit from Visual Studio Code extension in [Teams Toolkit (Preview) - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=TeamsDevApp.ms-teams-vscode-extension).
1. [Set up your dev environment](https://docs.microsoft.com/en-us/microsoftteams/platform/m365-apps/prerequisites).

The following are the steps to upgrade manifest and `TeamsJS` client SDK:

## Upgrade manifest

1. From Visual Studio Code, open command platte (Ctrl+Shift+P / ⌘⇧-P).
1. Type: `Teams: Upgrade Teams manifest to support Outlook and Office apps` in the search box.
1. Select Teams app manifest file.

This command will:

* Update manifest version to use the latest `m365DevPreview` version.
* Update manifest file to use the latest `DevPreview` schema.

For more information on required manifest schema and version, see [Developer Preview manifest schema](https://docs.microsoft.com/en-us/microsoftteams/platform/resources/schema/manifest-schema-dev-preview).

## Upgrade Teams JavaScript Client SDK

1. From Visual Studio Code, open command platte (Ctrl+Shift+P / ⌘⇧-P).
1. Type: `Teams: Upgrade Teams JS SDK references to support Outlook and Office apps` in the search box.
1. Select Teams app project folder.

This command will:

* `package.json` references to TeamsJS SDK Preview
* Import statements for TeamsJS SDK Preview
* [Function](https://docs.microsoft.com/en-us/microsoftteams/platform/m365-apps/using-teams-client-sdk-preview?tabs=manifest-teams-toolkit%2Cjavascript#apis-organized-into-capabilities) references for TeamsJS SDK Preview
* [Enum](https://docs.microsoft.com/en-us/microsoftteams/platform/m365-apps/using-teams-client-sdk-preview?tabs=manifest-teams-toolkit%2Cjavascript#apis-organized-into-capabilities) and [Interface](https://docs.microsoft.com/en-us/microsoftteams/platform/m365-apps/using-teams-client-sdk-preview?tabs=manifest-teams-toolkit%2Cjavascript#apis-organized-into-capabilities) references for TeamsJS SDK Preview
* `TODO` comment reminders to review areas that might be impacted by [Context](https://docs.microsoft.com/en-us/microsoftteams/platform/m365-apps/using-teams-client-sdk-preview?tabs=manifest-teams-toolkit%2Cjavascript#updates-to-the-context-interface) interface changes
* `TODO` comment reminders to ensure [conversion to promises functions from callback style functions](https://docs.microsoft.com/en-us/microsoftteams/platform/m365-apps/using-teams-client-sdk-preview?tabs=manifest-teams-toolkit%2Cjavascript#callbacks-converted-to-promises) has gone well at every call site the tool found

> [!IMPORTANT]
> Ensure that you review any of the `TODO` items deposited by the tool.
## Run your Teams application in Outlook and Office.com

After upgrading you can run Teams application in Outlook and Office.com.

### Upload and run your application in Teams

The following are the steps to Test your application in Outlook and Office.com by uploading your application through Teams client:

1. Push code changes to the server to host your application.
1. Move application to a zip folder.
1. Go to Teams client and select **Apps**.
1. Select **Upload a custom app** and select your application's zip folder.
1. Select **Add** on app details to install the application.

Teams install and launch your app. You can find your app in **More apps**.

 ![m365-app](https://user-images.githubusercontent.com/37978464/141746527-529498db-99b5-4166-be47-979bf8e6b099.png)

## Run Teams application in Outlook

Perform the following steps to preview personal tab apps in Outlook web app and desktop clients:

### Outlook web application

1. Go to https://outlook.office.com.
1. Select the three dots on the bottom left bar.

    ![apps](https://user-images.githubusercontent.com/37978464/141746576-8b8d4723-9189-4877-b391-2e463812c01c.png)

1. Select the name of your app to preview in Outlook web application.

    ![preview-outlook-web-application](https://user-images.githubusercontent.com/37978464/141746688-92203045-d320-4e34-a5eb-e9a21fd65877.png)

### Outlook desktop client

1. Open Outlook desktop client.
1. Select the three dots on the bottom left bar.

     ![outlook-desktop-apps](https://user-images.githubusercontent.com/37978464/141746859-29e27347-d2d8-4295-a21d-ad32e24b3d53.png)

1. Select the name of your app to preview it in Outlook Desktop Client.

     ![outlook-desktop-preview](https://user-images.githubusercontent.com/37978464/141746889-2e7565a4-f1ae-4de7-8f4d-238e7bdf5bbb.png)

## Run Teams application in Office.com

Perform the following steps to preview your apps in Outlook web client:

1. Go to www.office.com.
1. Select the three dots on the bottom left bar.

    ![m365-app](https://user-images.githubusercontent.com/37978464/141746527-529498db-99b5-4166-be47-979bf8e6b099.png)

1. Select the name of your app to preview it in `office.com`.

    ![office-preview](https://user-images.githubusercontent.com/37978464/141746981-172541a9-5c2c-42a5-a356-e9fa25fb3a28.png)

## Create a sample application with Teams Toolkit and Run it in Outlook and Office.com

Perform the following steps to create a new tab app using Teams Toolkit and run it in Outlook and Office.com:

1. Create a new sample Teams app in Visual Studio Code with Teams Toolkit, use command palette and run `Teams: Create a new Teams app` and select **Start from a sample**.

    ![sample-app](https://user-images.githubusercontent.com/37978464/141747054-62a8fddc-429f-4b67-b0a6-1d56906ff069.png)

1. Select **Todo List (Works in Teams, Outlook and Office)** or **NPM Search Connector** in the next window and click **OK**.

    ![sample-list](https://user-images.githubusercontent.com/37978464/141747102-1d4457c0-98f0-4ba4-ad8a-fc32307a4a45.png)

    This step will create a sample application. Once the project has been successfully created:

1. Select **Provision in the cloud**.

    ![provision-in-cloud](https://user-images.githubusercontent.com/37978464/141747144-667a75e0-370c-403e-8ebc-4e961f4de913.png)

1. Select **Deploy to the cloud**.

    ![deploy-to-the-cloud](https://user-images.githubusercontent.com/37978464/141747202-d559bc72-f57b-4ff9-a2b9-d3e313eb168f.png)

1. Select **Teams:Zip Teams metadata package**.

    ![zip-metadata-package](https://user-images.githubusercontent.com/37978464/141747243-bb97ab3e-028e-4ad0-bf95-3bf22d2b267f.png)
1. Follow the `README` file in the sample application project to run the application in Outlook and Office.com.

## See also

[Overview](https://docs.microsoft.com/en-us/windows/uwp/m365-apps/overview)