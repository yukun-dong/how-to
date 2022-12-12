# Add Message Extension capability to your Teams app

## Introduction

Message Extension allows users to interact with your web service while composing messages in the Microsoft Teams client. Users can invoke your web service to assist message composition, from the message compose box, or from the search bar.

Message Extensions are implemented on top of the Bot support architecture within Teams. Learn more from [Build message extensions for Teams
](https://learn.microsoft.com/en-us/microsoftteams/platform/messaging-extensions/what-are-messaging-extensions?tabs=dotnet).

## Prerequisites

To configure message extension as additional capability, please make sure:

- You have a Teams application and its manifest.
- You have a Microsoft 365 account to test the application.
- You have a message extension Teams app created using Teams Toolkit [Create a message extension app with Teams Toolkit](https://learn.microsoft.com/microsoftteams/platform/toolkit/create-new-project?pivots=visual-studio-code).

For adding message extension to a tab Teams app, please go to: 
[Add message extension to a tab Teams app](#add-message-extension-to-a-tab-teams-app)

For adding message extension to a bot Teams app, please go to: [Add message extension to a bot Teams app](#add-message-extension-to-a-bot-teams-app)

## Add message extension to a tab Teams app:

Following are the steps to add Message Extension capability to a tab app:
1. [Update manifest file](#configure-message-extension-capability-in-teams-application-manifest).
1. [Bring message extension code to your project](#bring-message-extension-code-to-your-project).
1. [Setup local debug environment](#setup-local-debug-environment).
1. [Move the application to Azure](#move-the-application-to-azure).

### Configure Message Extension capability in Teams application manifest
1. Copy the 'composeExtensions' section in `manifest.template.json` from your previously created message extension app to your own `manifest.template.json`. You can find it from `./appPackage` folder.

1. Add your bot domain to the `validDomains` field.
    Example:
    ```json
    "validDomains": [
        "${{BOT_DOMAIN}}"
    ],
    ```
    `BOT_ID` and `BOT_DOMAIN` are built-in variables of Teams Toolkit. They will be replaced with the true value in runtime based on your current environment(local, dev, etc.).

### Bring message extension code to your project
1. Copy all source code from previously created message extension app to your own Teams app. We suggest you to copy them into a `bot/` folder. Your folder structure will be like:
    ```
    |-- teasmfx/
    |-- infra/
    |-- appPackage/
    |-- bot/           <!--message extension source code-->
    |   |-- index.ts
    |   |-- config.ts
    |   |-- teamsBot.ts
    |   |-- package.json
    |-- src/            <!--your current source code-->
    |   |-- index.ts
    |-- package.json
    ```
    We suggest you to re-organize the folder structure and create a root package.json as:
     ```
    |-- teasmfx/
    |-- infra/
    |-- appPackage/
    |-- tabs/           <!--move your current source code to a new sub folder-->
    |   |-- src/
    |   |   |-- index.tsx
    |   |-- package.json
    |-- bot/            <!--message extension source code-->
    |   |-- index.ts
    |   |-- config.ts
    |   |-- teamsBot.ts
    |   |-- package.json
    |-- package.json <!--root package.json-->
    ```
1. Add following to your root package.json:
    ```json
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "install:bot": "cd bot && npm install",
      "install:tab": "cd tab && npm install",
      "install": "concurrently \"npm run install:bot\" \"npm run install:tab\"",
      "dev:bot": "cd bot && npm run dev",
      "start:tab": "cd tab && npm run start",
      "build:tab": "cd tab && npm run build",
      "build:bot": "cd bot && npm run build",
      "build": "concurrently \"npm run build:tab\" \"npm run build:bot\""
    },
    "devDependencies": {
        "@microsoft/teamsfx-run-utils": "alpha"
    },
    "dependencies": {
        "concurrently": "^7.6.0"
    },
    ```

### Setup local debug environment
1. Copy `./teamsfx/script/run.js` from message extension app to your Teams app, and rename scripts as below:
     ```
    |-- teasmfx/
    |   |-- script/
    |   |   |-- run.tab.js <!--your original script-->
    |   |   |-- run.bot.js <!--copied script-->
    ```
1. Update `run.tab.js` and `run.bot.js` following section:
    ```js
      // launch service locally by executing npm command
      cp.spawn(/^win/.test(process.platform) ? "npm.cmd" : "npm", ["run", "start:tab"], {
        stdio: "inherit",
      });
    ```

    ```js
      // launch service locally by executing npm command
      cp.spawn(/^win/.test(process.platform) ? "npm.cmd" : "npm", ["run", "dev:bot"], {
        stdio: "inherit",
      });
    ```

1. In `launch.json`, copy configuration with name "Attach to bot" into yours, then update compounds to be following:
    ```json
      {
          "name": "Debug (Edge)",
          "configurations": [
              "Attach to Frontend (Edge)",
              "Attach to Bot"
          ],
          "preLaunchTask": "Start Teams App Locally",
          "presentation": {
              "group": "all",
              "order": 1
          },
          "stopAll": true
      },
      {
          "name": "Debug (Chrome)",
          "configurations": [
              "Attach to Frontend (Chrome)",
              "Attach to Bot"
          ],
          "preLaunchTask": "Start Teams App Locally",
          "presentation": {
              "group": "all",
              "order": 2
          },
          "stopAll": true
      }
    ```



1. In `tasks.json`, copy the tasks with label "Start local tunnel", "Start bot" from message extension app into yours. Update "Validate prerequisites" "portOccupancy" to include 3978 (bot service port), 9239 (bot inspector port for Node.js debugger). Update the task with label "Start Teams App Locally" to be following:
    ```json
      {
        "label": "Start Teams App Locally",
        "dependsOn": [
            "Validate prerequisites",
            "Start local tunnel",
            "Create resources",
            "Set up local projects",
            "Start services"
        ],
        "dependsOrder": "sequence"
      }
    ```



1. Update `/teamsfx/app.local.yml`.
Add Below "provision" section into your `app.local.yml` file. You can also find this section from previously created message extension app.
    ```yaml
    provision:
      - uses: botAadApp/create # Creates a new AAD app for bot if BOT_ID environment variable is empty
        with:
          name: message-extension-bot
        # Output: following environment variable will be persisted in current environment's .env file.
        # BOT_ID: the AAD app client id created for bot
        # SECRET_BOT_PASSWORD: the AAD app client secret created for bot

      - uses: botFramework/create # Create or update the bot registration on dev.botframework.com
        with:
          botId: ${{BOT_ID}}
          name: message-extension-bot
          messagingEndpoint: ${{BOT_ENDPOINT}}/api/messages
          description: ""
    ```

1. Update `.vscode/task.json` tasks' `command` with label "Start bot" and "Start frontend" to the following:
    ```json
    {
      "label": "Start bot",
      "type": "shell",
      "command": "node teamsfx/script/run.bot.js . teamsfx/.env.local",
      ...
    }
    {
      "label": "Start frontend",
      "type": "shell",
      "command": "node teamsfx/script/run.tab.js . teamsfx/.env.local",
      ...
    }

    ```
1. Open the `Run and Debug Activity Panel` and select `Debug (Edge)` or `Debug (Chrome)`. Press F5 to preview your Teams app locally.
### Move the application to Azure

1. Manually merge the content in `infra/` and `teamsfx/app.yml` folder with yours.
1. Run `Teams: Provision in the cloud` command in Visual Studio Code to apply the bicep to Azure.

1. Run `Teams: Deploy to cloud` command in Visual Studio Code to deploy your app code to Azure.

1. Open the `Run and Debug Activity Panel` and select `Launch Remote (Edge)` or `Launch Remote (Chrome)`. Press F5 to preview your Teams app.

## Add message extension to a bot Teams app:
Since message extensions are implemented on top of the Bot support architecture within Teams. Adding Message Extension to a bot Teams app is simpler.

Following are the steps to add Message Extension capability to a bot app:
1. [Update manifest file](#configure-message-extension-capability-in-teams-application-manifest).
1. [Bring message extension code to your project](#bring-message-extension-code-to-your-project).

### Configure Message Extension capability in Teams application manifest
1. Copy the 'composeExtensions' section in `manifest.template.json` from previously created message extension app to your own `manifest.template.json`. You can find it from `./appPackage` folder.

1. Add your bot domain to the `validDomains` field.
    Example:
    ```
    "validDomains": [
        "${{BOT_DOMAIN}}"
    ],
    ```
    `BOT_ID` and `BOT_DOMAIN` are built-in variables of Teams Toolkit. They will be replaced with the true value in runtime based on your current environment(local, dev, etc.).

### Bring message extension code to your project
1. If you are adding message extension to a bot Teams app, then you should already have a class that extends `TeamsActivityHandler`. Copy all methods from `TeamsBot` class in "teamsBot.ts" from your previously created message extension app, and append them to your own class. After this your own class will be like:

    ```ts
      public class YourHandler extends TeamsActivityHandler{
        /**
         * your own code
        */

        //message extension code
        // Action.
        public async handleTeamsMessagingExtensionSubmitAction(
        context: TurnContext,
        action: any
      ): Promise<any> {}

        // Search.
        public async handleTeamsMessagingExtensionQuery(context: TurnContext, query: any): Promise<any> {}

        public async handleTeamsMessagingExtensionSelectItem(
          context: TurnContext,
          obj: any
        ): Promise<any> {}

        // Link Unfurling.
        public async handleTeamsAppBasedLinkQuery(context: TurnContext, query: any): Promise<any> {}
      }
    ```
