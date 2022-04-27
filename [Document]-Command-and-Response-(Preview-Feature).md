# Build command and response

> Please be advised these features are currently under active development, with a lot of changes taking place. Please expect breaking changes as we continue to iterate.
We really appreciate your feedback! If you encounter any issue or error, please report issues to us [here](https://github.com/OfficeDev/TeamsFx/issues/new/choose).

> How to enable preview features
> 1. Upgrade to the latest [Teams Toolkit](https://marketplace.visualstudio.com/items?itemName=TeamsDevApp.ms-teams-vscode-extension).
> 1. Open Visual Studio Code and find `Manage` icon from sidebar (Bottom Left) 
> 1. Select `Settings` and find `Teams Toolkit` under `Extensions` section.
> 1. Tick the checkbox for `Enable Preview Features`.
> 1. Restart Visual Studio Code.

Microsoft Teams allows you to automate simple and repetitive tasks right inside a conversation. You can build a Teams bot that can respond to simple commands sent in chats with adaptive cards.

In this tutorial, you will learn:
* [How to create a command bot with Teams Toolkit](#How-to-create-a-command-response-bot)
* [How to understand command bot project](#Take-a-tour-of-your-app-source-code)
* [How to add more command and response](#How-to-add-more-command-and-response)
* [How command and response bot works](#How-command-and-response-works)
* [How to connect to and existing API](#Connect-to-existing-API)
* [How to build dynamic content in response with adaptive cards](#How-to-build-command-response-using-adaptive-card-with-dynamic-content)
* [How to extend my notification bot to support command and response](#how-can-i-extend-my-notification-bot-to-support-command-and-response)

## How to create a command-response bot

### In Visual Studio Code

1. From Teams Toolkit sidebar click `Create a new Teams app` or select `Teams: Create a new Teams app` from command palette.

![image](https://user-images.githubusercontent.com/11220663/165435370-99aa79b8-044f-44ea-b2a9-e42a055a3f6c.png)

2. Select `Create a new Teams app`.

![image](https://user-images.githubusercontent.com/11220663/165435420-566f8b99-ab44-482e-ba5f-857f80af4081.png)

3. Select the `Command bot` from Scenario-based Teams app section.

![image](https://user-images.githubusercontent.com/11220663/165442739-16ecd6da-ac09-4f8e-b6e8-3452905e2f22.png)

4. Select programming language

![image](https://user-images.githubusercontent.com/11220663/165435816-e6d46074-6e0a-4186-804b-c83bfbe12b6f.png)

5. Enter an application name and then press enter.

![image](https://user-images.githubusercontent.com/11220663/165435852-686deaef-119e-4311-9343-d8ef4b335516.png)

### In TeamsFx CLI

* If you prefer interactive mode, execute `teamsfx new` command, then use the keyboard to go through the same flow as in Visual Studio Code.

* If you prefer non-interactive mode, enter all required parameters in one command.

`teamsfx new --interactive false --capabilities "command-and-response" --programming-language "typescript" --folder "./" --app-name myAppName`

<p align="right"><a href="#Build-command-and-response">back to top</a></p>

## Take a tour of your app source code
After scaffolding or adding a command-response bot, you will find your bot's source code under `bot/` folder:
| File name | Contents |
|- | -|
|`bot/src/internal/initialize.ts`| Create the global bot adapter and initialize with Teams Frameworks|
|`bot/src/index.ts`| Server code to host the bot app and listen on `/api/messages` to process Teams activity with Bot Framework |
|`bot/src/helloworldCommandHandler.ts`| A hello world command handler to process a `helloworld` command and return an adaptive card as response |
|`bot/src/adaptiveCards/*.json`| Adaptive card JSON file used as your command response |

<p align="right"><a href="#Build-command-and-response">back to top</a></p>

## How to add more command and response
1. Add new command handler to your bot
    1. Add a .ts/.js file (e.g. `xxxCommandHandler.ts`) under `bot/src` to handle your bot command, and include the following boilerplate code to get-started:   
        ```typescript
        import { Activity, TurnContext } from "botbuilder";
        import { CommandMessage, TeamsFxBotCommandHandler, TriggerPatterns } from "@microsoft/teamsfx";
        import { MessageBuilder } from "@microsoft/teamsfx";

        export class xxxCommandHandler implements TeamsFxBotCommandHandler {
            triggerPatterns: TriggerPatterns = "<string or RegExp pattern to trigger the command>";

            async handleCommandReceived(
                context: TurnContext,
                message: CommandMessage
            ): Promise<string | Partial<Activity>> {
                // verify the command arguments which are received from the client if needed.
                console.log(`Bot received message: ${message.text}`);

                // do something to process your command and return message activity as the response.
                // You can leverage `MessageBuilder` utilities from the `@microsoft/teamsfx` SDK 
                // to facilitate building message with cards supported in Teams.
            }    
        }
        ```

    1. Provide the `triggerPatterns` that can trigger this command handler. Usally it's the command name defined in your manifest, or you can use RegExp to handle a complex command (e.g. with some options in the command message).

    1. Implement `handleCommandReceived` to handle the command and return a response that will be used to notify the end users. 
        * You can retrieve useful information for the conversation from the `context` parameter if needed.
        * Parse command input if needed: 
          * `message.text`: the use input message
          * `message.macthes`: the capture groups if you uses the RegExp for `triggerPatterns` to trigger the command.

        * You can build your response data in text format or follow the steps bellow to use adaptive card to render rich content in Teams:
            * Prepare your adaptive card content in a JSON file（e.g. myCard.json) under the `bot/adaptiveCards` folder, here is a sample adaptive card JSON payload:
                ```json
                {
                    "type": "AdaptiveCard",
                    "body": [
                    {
                        "type": "TextBlock",
                        "size": "Medium",
                        "weight": "Bolder",
                        "text": "Your Hello World Bot is Running"
                    },
                    {
                        "type": "TextBlock",
                        "text": "Congratulations! Your hello world bot is running. Click the documentation below to learn more about Bots and the Teams Toolkit.",
                        "wrap": true
                    }
                    ],
                    "actions": [
                    {
                        "type": "Action.OpenUrl",
                        "title": "Bot Framework Docs",
                        "url": "https://docs.microsoft.com/en-us/azure/bot-service/?view=azure-bot-service-4.0"
                    },
                    {
                        "type": "Action.OpenUrl",
                        "title": "Teams Toolkit Docs",
                        "url": "https://aka.ms/teamsfx-docs"
                    }
                    ],
                    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
                    "version": "1.4"
                }
                ```

            * Import your card content into your code file where your command handler exists:
              ```typescript
              import myCard from "./adaptiveCards/myCard.json"
              ```

            * In your `handleCommandReceived` API, use `MessageBuilder.attachAdaptiveCardWithoutData` or `MessageBuilder.attachAdaptiveCard` to build a bot message activity with the adaptive card and return the message.
                ```typescript              
                return MessageBuilder.attachAdaptiveCardWithoutData(myCard);
                ```

            > Note: If you'd like to send adaptive card with dynamic data, please refer to [this section](#how-to-build-command-response-using-adaptive-card-with-dynamic-content).
  

1. Register your command handler to the underlying bot.

   Open `bot\src\internal\initialize.ts`: 
   
   * update the call to `ConversationBot` constructor to include your new added command handlers.

        ```typescript
        export const commandBot = new ConversationBot({
            // The bot id and password to create BotFrameworkAdapter.
            // See https://aka.ms/about-bot-adapter to learn more about adapters.
            adapterConfig: {
                appId: process.env.BOT_ID,
                appPassword: process.env.BOT_PASSWORD,
            },
            command: {
                enabled: true,
                commands: [ new HelloWorldCommandHandler(), new xxxCommandHandler() ],
            },
        });
        ```
    * Or, use `registerCommand`/ `registerCommands` API from your `ConverrsationBot` instance to incrementally add your command(s) to a command bot.

       ```
       // register a single command
       commandBot.command.registerCommand(new xxxCommandHandler());
       
       // register a set of commands
       commandBot.command.registerCommands([
           new xxxCommandHandler(),
           new yyyCommandHandler()
       ]);
       ```

1. Add command definition in manifest. You can edit the manifest template file `templates\appPackage\manifest.template.json` to include:
    * The command `title` that user type in the message compose area to trigger the command.
    * The `command` description for this command.

      ![manifest-add-command](https://user-images.githubusercontent.com/10163840/160374446-7fd164d6-63c9-47b2-9bf1-0d6a88731e8d.png)

Now, you are all done with the code development of adding a new command and response into your bot app. You can just press `F5` to loca debug with the command-response bot, or use provision and deploy command to deploy the change to Azure.

<p align="right"><a href="#Build-command-and-response">back to top</a></p>

## How command-and-response works
The TeamsFx Command-Response Bots are created using the [Bot Framework SDK](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0). The Bot Framework SDK provides [built-in message handler](https://docs.microsoft.com/en-us/microsoftteams/platform/bots/bot-basics?tabs=javascript#teams-activity-handlers) to handle the incoming message activity, which requires learning curve to understand the concept of Bot Framework (e.g. the [event-driven conversation model](https://docs.microsoft.com/en-us/azure/bot-service/bot-activity-handler-concept?view=azure-bot-service-4.0&tabs=javascript)). To simplify the development, the TeamsFx SDK provides command-response abstraction layer to let developers only focus on the development of business logic to handle the command request without learning the Bot Framework SDK.

Behind the scenes, the TeamsFx SDK leverages [Bot Framework Middleware](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-middleware?view=azure-bot-service-4.0) to handle the integration with the underlying activity handlers. This middleware handles the incoming message activity and invokes the corresponding `handlerCommandReceived` function if the received message text matches the command pattern provided in a `TeamsFxBotCommandHandler` instance. After processing, the middleware will call `context.sendActivity` to send the command response returned from the `handlerCommandReceived` function to the user.

<p align="right"><a href="#Build-command-and-response">back to top</a></p>

## Connect to existing API

If you want to invoke external APIs in your code but do not have the appropriate SDK, the "Teams: Connect to an API" command in Teams Toolkit VS Code extension or "teamsfx add api-connection" command in TeamsFx CLI would be helpful to bootstrap code to call target APIs. For more information, you can visit [Connect existing API document](https://aka.ms/teamsfx-connect-api).

<p align="right"><a href="#Build-command-and-response">back to top</a></p>

## Frequently Asked Questions

### How to build command response using adaptive card with dynamic content?
Adaptive card provides [Template Language](https://docs.microsoft.com/en-us/adaptive-cards/templating/) to allow users to render dynamic content with the same layout (the template). For example, use the adaptive card to render a list of items (todo items, assigned bugs, etc) that could varies according to different user. 

1. Add your adaptive card template JSON file under `bot/adativeCards` folder
1. Import the card template to you code file where your command handler exists (e.g. `myCommandHandler.ts`)
1. Model your card data
1. Use `MessageBuilder.attachAdaptiveCard` to render the template with dynamic card data

<p align="right"><a href="#Build-command-and-response">back to top</a></p>

### How can I extend my notification bot to support command and response?
1. Go to `bot\src\internal\initialize.ts(js)`, update your `conversationBot` initialization to enable command-response feature:

   ![enable-command](https://user-images.githubusercontent.com/10163840/165430233-04648a2a-d637-41f0-bb17-b34ddbd609f7.png)

1. Follow [this instruction](#How-to-add-more-command-and-response) to add command to your bot.

<p align="right"><a href="#Build-command-and-response">back to top</a></p>

### How can I extend my command and response bot to support notification?
1. Go to `bot\src\internal\initialize.ts(js)`, update your `conversationBot` initialization to enable notification feature:

    ![enable-notification](https://user-images.githubusercontent.com/10163840/165462039-12bd4f61-3fc2-4fc8-8910-6a4b1e138626.png)

2. Follow [this intruction](https://github.com/OfficeDev/TeamsFx/wiki/%5BDocument%5D-Notification-(Preview-feature)#notify) to send notification to the bot installation target (channel/group chat/personal chat). To quickly add a sample notification triggered by a HTTP endpoint, you can add the following sample code in `bot\src\index.ts(js)`:

    ```typescript
    server.post("/api/notification", async (req, res) => {
      for (const target of await commandBot.notification.installations()) {
        await target.sendMessage("This is a sample notification message");
      }
    
      res.json({});
    });

3. Uninstall your previous bot installation from Teams, and re-run local debug to test your bot notification. Then you can send a notification to the bot installation targets (channel/group chat/personal chat) by using a HTTP POST request with taret URL `https://localhost:3978/api/notification`.
    ```
    curl -X POST https://localhost:3978/api/notification
    ```

To explore more details of the notification feature (e.g. send notification with adaptive card, add more triggers), you can further refer to [the notification document](https://aka.ms/teamsfx-notification).
<p align="right"><a href="#Build-command-and-response">back to top</a></p>