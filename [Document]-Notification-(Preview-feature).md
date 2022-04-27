# Send notification to Teams

> Please be advised these features are currently under active development, with a lot of changes taking place. Please expect breaking changes as we continue to iterate.
We really appreciate your feedback! If you encounter any issue or error, please report issues to us [here](https://github.com/OfficeDev/TeamsFx/issues/new/choose).

> How to enable preview features
> 1. Upgrade to the latest [Teams Toolkit](https://marketplace.visualstudio.com/items?itemName=TeamsDevApp.ms-teams-vscode-extension).
> 1. Open Visual Studio Code and find `Manage` icon from sidebar (Bottom Left) 
> 1. Select `Settings` and find `Teams Toolkit` under `Extensions` section.
> 1. Tick the checkbox for `Enable Preview Features`.
> 1. Restart Visual Studio Code.

Notification in Teams means you can proactively message an individual person, a chat, or a channel via plain text or different [cards](https://docs.microsoft.com/en-us/microsoftteams/platform/task-modules-and-cards/cards/cards-reference).

In this tutorial, you will learn:
* [Different approaches to send notifications to Teams](#Teams-bot-application-or-Teams-incoming-webhook)
* [Send notification to Teams via Teams bot application](#Notification-via-Teams-bot-application)
  * [How to create a new notification bot with Teams Toolkit](#Create-a-new-Notification-Project)
  * [How to understand notification bot project](#Take-a-tour-of-your-app-source-code)
  * [How to send more notifications](#How-to-send-more-notifications)
  * [How to add authentication for your notification API](#Add-authentication-for-your-notification-API)
  * [How notification works](#How-notification-works)
  * [How to connect to an existing API](#Connect-to-existing-API)
* [Send notification to Teams via Teams incoming webhook](#Notification-via-Incoming-Webhook)

# Teams bot application or Teams incoming webhook
Microsoft Teams Framework (TeamsFx) supports two major ways to help you send notifications from your system to Teams by creating a Teams Bot Application or Teams Incoming Webhook.

Here's the comparison of the two approaches to help you make the decision.

| | **Teams Bot App** | **Teams Incoming Webhook** |
| - | - | - |
| Able to message individual person | Yes | No |
| Able to message group chat | Yes | No |
| Able to message public channel | Yes | Yes |
| Able to message private channel | No | Yes |
| Able to send card message | Yes | Yes |
| Able to send welcome message | Yes | No |
| Able to retrieve Teams context | Yes | No |
| Require installation step on Teams | Yes | No |
| Require Azure resource | Azure Bot Service | No |

<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>

# Notification via Teams bot application
## Create a new notification project

### In Visual Studio Code
1. From Teams Toolkit side bar click `Create a new Teams app` or select `Teams: Create a new Teams app` from the command palette.

![image](https://user-images.githubusercontent.com/11220663/165435370-99aa79b8-044f-44ea-b2a9-e42a055a3f6c.png)

2. Select `Create a new Teams app`.
  
![image](https://user-images.githubusercontent.com/11220663/165435420-566f8b99-ab44-482e-ba5f-857f80af4081.png)

3. Select `Notification bot` from Scenario-based Teams app section.

![image](https://user-images.githubusercontent.com/11220663/165435543-a2de8080-9467-4dee-93e7-11f08b1b2245.png)

4. Select triggers. You can choose from `HTTP Trigger` or `Timer Trigger`. The triggers are based on `Restify Server` (means the created app code is a restify web app) or `Azure Functions` (means the created app code is Azure Functions).
  
![image](https://user-images.githubusercontent.com/11220663/165435740-cd795eb4-8723-4ad0-b7a2-91ebb49180e8.png)

5. Select programming language

![image](https://user-images.githubusercontent.com/11220663/165435816-e6d46074-6e0a-4186-804b-c83bfbe12b6f.png)

6. Enter an application name and then press enter.

![image](https://user-images.githubusercontent.com/11220663/165435852-686deaef-119e-4311-9343-d8ef4b335516.png)


### In TeamsFx CLI
* If you prefer interactive mode, execute `teamsfx new` command, then use the keyboard to go through the same flow as in Visual Studio Code.

* If you prefer non-interactive mode, enter all required parameters in one command.

`teamsfx new --interactive false --capabilities "notification" --bot-host-type-trigger "http-restify" --programming-language "typescript" --folder "./" --app-name MyAppName`

<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>

## Take a tour of your app source code

The created app is a normal TeamsFx project that will contain following folders:

| Folder | Contents |
| - | - |
| `.fx` | Project level settings and configurations |
| `.vscode` | VSCode files for local debug |
| `bot` | The bot source code |
| `templates` |Templates for Teams app manifest and corresponding Azure resources|

### Restify hosted Bot

If you select `(Restify)` trigger(s), the `bot/` folder is restify web app with following content:

| File / Folder | Contents |
| - | - |
| `src/adaptiveCards/` | Adaptive card templates |
| `src/internal/` | Generated initialize code for notification functionality |
| `src/cardModels.*s` | Adaptive card data models |
| `src/index.*s` | The entrypoint to handle bot messages and send notifications |
| `.gitignore` | The git ignore file to exclude local files from bot project |
| `package.json` | The NPM package file for bot project |

### Azure Functions hosted Bot

If you select `(Azure Functions)` trigger(s), the `bot/` folder is azure functions app with following content:

| File / Folder | Contents |
| - | - |
| `messageHandler/` | The function to handle bot messages |
| `*Trigger/` | The function to trigger notification |
| `src/adaptiveCards/` | Adaptive card templates |
| `src/internal/` | Generated initialize code for notification functionality |
| `src/cardModels.*s` | Adaptive card data models |
| `src/*Trigger.*s` | The entrypoint of each notification trigger |
| `.funcignore` | The azure functions ignore file to exclude local files |
| `.gitignore` | The git ignore file to exclude local files from bot project |
| `host.json` | The azure functions host file |
| `local.settings.json` | The azure functions local setting file |
| `package.json` | The NPM package file for bot project |

<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>

## How to send more notifications

### Initialize

To send notification, you need to create `ConversationBot` first. (Code already generated at `bot/src/internal/initialize.*s`)

``` typescript
const bot = new ConversationBot({
    // The bot id and password to create BotFrameworkAdapter.
    // See https://aka.ms/about-bot-adapter to learn more about adapters.
    adapterConfig: {
        appId: process.env.BOT_ID,
        appPassword: process.env.BOT_PASSWORD,
    },
    // Enable notification
    notification: {
        enabled: true,
    },
});
```

### Customize Adapter

You can initialize with your own adapter, or customize after initialization.

``` typescript
// Create your own adapter
const adapter = new BotFrameworkAdapter(...);

// Customize your adater, e.g., error handling
adapter.onTurnError = ...

const bot = new ConversationBot({
    // use your own adapter
    adapter: adapter;
    ...
});

// Or, customize later
bot.adapter.onTurnError = ...
```

### Customize Storage

You can initialize with your own storage. This storage will be used to persist notification connections.

``` typescript
// implement your own storage
class MyStorage implements NotificationTargetStorage {...}
const myStorage = new MyStorage(...);

// initialize ConversationBot with notification enabled and customized storage
const bot = new ConversationBot({
    // The bot id and password to create BotFrameworkAdapter.
    // See https://aka.ms/about-bot-adapter to learn more about adapters.
    adapterConfig: {
        appId: process.env.BOT_ID,
        appPassword: process.env.BOT_PASSWORD,
    },
    // Enable notification
    notification: {
        enabled: true,
        storage: myStorage,
    },
});
```

> Note: It's recommended to use your own shared storage for production environment. If `storage` is not provided, a default local file storage will be used, which stores notification connections into:
>   - *.notification.localstore.json* if running locally
>   - *${process.env.TEMP}/.notification.localstore.json* if `process.env.RUNNING_ON_AZURE` is set to "1"

### Notify

A Teams bot can be installed into a team, or a group chat, or as personal app, depending on difference scopes.

To send notification in team/channel:
``` typescript
// list all installation targets
for (const target of await bot.notification.installations()) {
    // "Channel" means this bot is installed to a Team (default to notify General channel)
    if (target.type === "Channel") {
        // Directly notify the Team (to the default General channel)
        await target.sendAdaptiveCard(...);

        // List all members in the Team then notify each member
        const members = await target.members();
        for (const member of members) {
            await member.sendAdaptiveCard(...);
        }

        // List all channels in the Team then notify each channel
        const channels = await target.channels();
        for (const channel of channels) {
            await channel.sendAdaptiveCard(...);
        }
    }
}
```

To send notification in group chat
``` typescript
// list all installation targets
for (const target of await bot.notification.installations()) {
    // "Group" means this bot is installed to a Group Chat
    if (target.type === "Group") {
        // Directly notify the Group Chat
        await target.sendAdaptiveCard(...);

        // List all members in the Group Chat then notify each member
        const members = await target.members();
        for (const member of members) {
            await member.sendAdaptiveCard(...);
        }
    }
}
```

To send notification in personal chat
``` typescript
// list all installation targets
for (const target of await bot.notification.installations()) {
    // "Person" means this bot is installed as Personal app
    if (target.type === "Person") {
        // Directly notify the individual person
        await target.sendAdaptiveCard(...);
    }
}
```

<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>

## Add authentication for your notification API

If you choose http trigger, the scaffolded notification API does not have authentication / authorization enabled. We suggest you add authentication / authorization for this API before using it for production purpose. Here're some common ways to add authentication / authorization for an API:

1. Use an API Key. If you chose Azure Functions to host your notification bot, it already provides [function access keys](https://docs.microsoft.com/en-us/azure/azure-functions/security-concepts?tabs=v4#function-access-keys), which may be helpful to you.

2. Use an access token issued by [Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/authentication/)

There would be more authentication / authorization solutions for an API. You can choose the one that satisfies your requirement best.

<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>

## How notification works

Technically, Bot Framework SDK provides the functionality to [proactively message in Teams](https://docs.microsoft.com/microsoftteams/platform/bots/how-to/conversations/send-proactive-messages?tabs=typescript). And TeamsFx SDK provides the functionality to manage bot's conversation references when bot event is triggered.

Current TeamsFx SDK recognize following bot events:

| Event | Behavior |
| - | - |
| Bot is installed | Add the target conversation reference to storage |
| Bot is uninstalled | Remove the target conversation reference from storage |
| Team that bot installed in is deleted | Remove the target conversation reference from storage |
| Team that bot installed in is restored | Add the target conversation reference to storage |

When notifying, TeamsFx SDK creates new conversation from the selected conversation reference and send messages. Or, for advanced usage, you can directly access the conversation reference to execute your own bot logic:
``` typescript
// list all installation targets
for (const target of await bot.notification.installations()) {
    // call Bot Framework's adapter.continueConversation()
    await target.adapter.continueConversation(target.conversationReference, async (context) => {
        // your own bot logic
        await context...
    });
}
```

<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>

## Connect to existing API

If you want to invoke external APIs in your code but do not have the appropriate SDK, the "Teams: Connect to an API" command in Teams Toolkit VS Code extension or "teamsfx add api-connection" command in TeamsFx CLI would be helpful to bootstrap code to call target APIs. For more information, you can visit [Connect existing API document](https://aka.ms/teamsfx-connect-api).

<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>

## Frequently Asked Questions

### How to add more triggers?

It depends on your host type.

- If you created Restify notification project, you can add HTTP trigger(s) by creating new routing

  ``` typescript
  server.post("/api/new-trigger", ...);
  ```

  Or add Timer trigger(s) via widely-used npm packages such as [cron](https://www.npmjs.com/package/cron), [node-schedule](https://www.npmjs.com/package/node-schedule), etc.

  Or add other trigger(s) via other packages.

- If you created Azure Functions notification project, you can add any Azure Functions trigger(s) with your own `function.json` file and code file(s). [Azure Functions supported triggers](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings?tabs=javascript#supported-bindings).

### Why notification target is lost after restart / redeploy the bot app?

Notification target connections are stored in the persistence storage. If you are using the default local file storage, Azure Web App and Azure Functions will clean up the local file when restart / redeploy.

It's recommended to use your own shared storage for production environment. See [Customize Storage](#customize-storage).

Or, as a workaround, after restart / redeploy, you can mention your bot in Teams chat / group / channel to re-add connections to the storage.

### Can I know all the targets my bot is installed in, out of the notification project?

There are [Microsoft Graph APIs](https://docs.microsoft.com/graph/api/team-list-installedapps) to list apps installed in a team / group / chat. So it may require you to iterate all your teams / groups / chats to get all the targets a certain app is installed in.

In notification project, it uses persistence storage to store installation targets. See [How Notification Works](#how-notification-works) for more information.

### How to customize the azurite listening ports?
If azurite exits due to port in use, you can [specify another listening port](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azurite?tabs=visual-studio#blob-listening-port-configuration) and update the [connection string](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azurite?tabs=visual-studio#http-connection-strings
) of `AzureWebJobsStorage` in `bot/local.settings.json`

<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>

# Notification via Incoming Webhook
Incoming Webhooks help in posting messages from apps to Teams. If Incoming Webhooks are enabled for a team in any channel, it exposes the HTTPS endpoint, which accepts correctly formatted JSON and inserts the messages into that channel. For example, you can create an Incoming Webhook in your DevOps channel, configure your build, and simultaneously deploy and monitor services to send alerts.

Teams Framework has built a [sample](https://github.com/OfficeDev/TeamsFx-Samples/tree/ga/incoming-webhook-notification) that walks you through:
* How to create an incoming webhook in Teams.
* How to send notifications using incoming webhooks with adaptive cards.


<p align="right"><a href="#Send-notification-to-Teams">back to top</a></p>
