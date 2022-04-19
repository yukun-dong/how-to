## Overview

Single sign-on (SSO) authentication in Microsoft Azure Active Directory (Azure AD) silently refreshes the authentication token to minimize the number of times users need to enter their sign in credentials. If users agree to use your app, they don't have to provide consent again on another device as they're signed in automatically. Teams Tabs and bots have similar flow for SSO support. You can refer to [Tab SSO](https://docs.microsoft.com/en-us/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso?tabs=dotnet) and [Bot SSO](https://docs.microsoft.com/en-us/microsoftteams/platform/bots/how-to/authentication/auth-aad-sso-bots) for more info.

## How SSO Works in TeamsFx Projects

*You can find TeamsFx samples with SSO in the [repo](https://github.com/OfficeDev/TeamsFx-Samples/tree/v2)*

You can find detailed info for SSO in Teams app [here (for Tab)](https://docs.microsoft.com/en-us/microsoftteams/platform/tabs/how-to/authentication/auth-aad-sso?tabs=dotnet) and [here (for Bot)](https://docs.microsoft.com/en-us/microsoftteams/platform/bots/how-to/authentication/auth-aad-sso-bots).

[TeamsFx SDK](https://www.npmjs.com/package/@microsoft/teamsfx) provides simplified experience for enabling SSO in TeamsFx projects. For detail, you can refer to [Readme](https://github.com/OfficeDev/TeamsFx/tree/dev/packages/sdk#user-identity).

If you want to add SSO to your project (including: Bot (hosting on Azure App Service), Messaging Extension, Static Launch Page, Embed existing web app), follow the following steps to add SSO feature to your TeamsFx projects.
- From Visual Studio Code: open the command palette and select: `Teams: Add SSO`.
- From TeamsFx CLI: run command `teamsfx add sso` in your project directory.

## What we will do in 'Add SSO' command

After you triggered `Add SSO` command from Visual Studio Code command palette or cli, we will do the following things:

1. Clareate Azure AD app tempte under `template\appPackage\aad.template.json`.

    This file will be used for provisioning Azure AD app. You can provision the Azure AD app with `Provision in the cloud` command or `Deploy AAD manifest` command.

1. Add `webApplicationInfo` object in Teams App manifest.

    This field is required in Teams App manifest when you want to enable SSO in your Teams app. You can preview the Teams App manifest (`template\appPackage\manifest.template.json`) after provisioning the Azure AD app.

1. [For Tab] Create `README.md` and sample code under `auth/tab/`

1. [For Bot] Create `README.md` and sample code under `auth/bot/`

## What you need to do after triggering 'Add SSO' command

You can follow the steps below to add SSO in your Teams app.

### Update your source code in Tab

*This part is only for projects with Tab. For projects with Bot only, please skip this part and go to [Bot part](#update-your-source-code-in-bot).*

There are two folders under `auth/tab`: `public` and `sso`.

1. In `public`, there are two html files which is used for authentication. You can simply copy the files under the folder and place it under `tabs/public/`.

1. In `sso`, there is one file. You can simply copy the folder and place it under `tabs/src/sso/`.
    - `GetUserProfile`: This file implement a function that calls Microsoft Graph API to get user info.
    - You need to manually run the following commands under `tabs/`:
        ```
        npm install @microsoft/teamsfx-react
        ```
    - You need to manually add the following lines to `tabs/src/components/sample/Welcome.tsx` to import `GetUserProfile`:

        ```
        import { GetUserProfile } from "../sso/GetUserProfile";
        ```
        and replace the following line:
        ```
        <AddSSO />
        ```
        with:
        ```
        <GetUserProfile />
        ```

### Update your source code in Bot

*This part is only for projects with Bot. For projects with Tab only, please skip this part.*

There are two folders under `auth/bot`: `public` and `sso`.

1. In `public`, there are two html files which is used for authentication. You can simply copy the folder and place it under `bot/src`. Note that you need to modify `bot/src/index` file to add routing to these pages.

1. In `sso`, there are three files. You can simply copy the folder and place it under `bot/src`.
    - `showUserInfo`: This file implement a function to get user info with SSO token. You can follow this method and create your own method that requires SSO token.
    - `ssoDialog`: Create a [ComponentDialog](https://docs.microsoft.com/en-us/javascript/api/botbuilder-dialogs/componentdialog?view=botbuilder-ts-latest) that used for SSO.
    - `teamsSsoBot`: Create a [TeamsActivityHandler](https://docs.microsoft.com/en-us/dotnet/api/microsoft.bot.builder.teams.teamsactivityhandler?view=botbuilder-dotnet-stable) with `ssoDialog` and add `showUserInfo` as a command that can be triggered. You need to register your own command with `addCommand` in this file.

1. After adding the following files, you need to new a `teamsSsoBot` instance in `bot/src/index` file. Please replace the following code:
    ```
    // Process Teams activity with Bot Framework.
    server.post("/api/messages", async (req, res) => {
        await commandBot.requestHandler(req, res);
    });
    ```

    with:

    ```
    const hanlder = new TeamsSsoBot();
    // Process Teams activity with Bot Framework.
    server.post("/api/messages", async (req, res) => {
        await commandBot.requestHandler(req, res, async (context)=> {
            await hanlder.run(context);
        });
    });
    ```

1. As mentioned above, you also need to add routing in `bot/src/index` file as below:

    ```
    server.get(
        "/auth-*.html",
        restify.plugins.serveStatic({
            directory: path.join(__dirname, "public"),
        })
    );
    ```

### Provision Azure AD app and deploy latest code

After running `Add SSO` command and updating source code, you need to run `Provision` + `Deploy` or `Local Debug` again to provision an Azure AD app for SSO. After the above steps, SSO is successfully added in your Teams App.