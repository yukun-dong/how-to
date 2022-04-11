# Command and Response with Teams Bot
A command and response bot is an app that responds to simple commands sent in Teams chat and replies a result in meaningful ways.

## How to create a command-response bot
In Visual Studio Code:

1. Open Teams Toolkit and select the Teams Toolkit ![teams-toolkit-sidebar-icon](https://user-images.githubusercontent.com/10163840/160794831-e0a370ce-888f-4176-bb26-f16a64b72118.png) icon in the Visual Studio Code sidebar or select `Teams: Create a new Teams app` from command palette.

1. Select `Create a new Teams app`.
    ![create-new-project](https://user-images.githubusercontent.com/10163840/160793793-630fe4dd-ff92-4d43-8bf4-47c12a10e0b5.png)

1. Select the `Command and Response` capability
    ![select-command-response](https://user-images.githubusercontent.com/10163840/160793837-7bb11e26-7608-44ca-8f06-ee7c9597a0f7.png)
    
1. Select `TypeScript` as the programming language.
    ![select-typescript](https://user-images.githubusercontent.com/10163840/160793855-8c5f5821-5dd5-4194-8aa4-17844ae720df.png)

1. Provide your app name and click `OK`.
    ![provide-app-name](https://user-images.githubusercontent.com/10163840/160793811-014c9b88-4fc8-4785-bb51-a861f7433c91.png)

In CLI, use the `teamsfx new` command: 

- By default, `teamsfx new` goes into interactive mode and guides you through the process of creating a new Teams application.
    ![cli-create-new](https://user-images.githubusercontent.com/10163840/160799625-6f956a40-0525-4906-8740-592384b7a9a8.png)
- Or, if you prefer non-interactive mode, enter all required parameters in one command
    ```
    teamsfx new --interactive false --capabilities "command-and-response" --programming-language "typescript" --folder "./" --app-name myAppName
    ```

## Take a tour of your app source code
After scaffolding or adding a command-response bot, you will find your bot's source code under `bot/` folder:
| File name | Contents |
|- | -|
|`bot/src/internal/initialize.ts`| Create the global bot adapter and initialize with Teams Frameworks|
|`bot/src/index.ts`| Server code to host the bot app and listen on `/api/messages` to process Teams activity with Bot Framework |
|`bot/src/helloworldCommandHandler.ts`| A hello world command handler to process a `helloworld` command and return an adaptive card as response |
|`bot/src/adaptiveCards/*.json`| Adaptive card JSON file used as your command response |

## How to add more command and response
1. Add command definition in manifest. You can edit the manifest template file `templates\appPackage\manifest.template.json` to include:
    * The command `title` that user type in the message compose area to trigger the command.
    * The `command` description for this command.

      ![manifest-add-command](https://user-images.githubusercontent.com/10163840/160374446-7fd164d6-63c9-47b2-9bf1-0d6a88731e8d.png)

1. Handle command in your bot
    1. Add a .ts/.js file (e.g. `xxxCommandHandler.ts`) under `bot/src` to handle your bot command, and include the following boilerplate code to get-started:   
        ```typescript
        import { Activity, TurnContext } from "botbuilder";
        import { CommandMessage, TeamsFxBotCommandHandler, TriggerPatterns } from "@microsoft/teamsfx";
        import { MessageBuilder } from "@microsoft/teamsfx";

        export class xxxCommandHandler implements TeamsFxBotCommandHandler {
            triggerPatterns: TriggerPatterns = "string or RegExp pattern to trigger the command";

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

Now, you are all done with the code development of adding a new command and response into your bot app. You can just press `F5` to loca debug with the command-response bot, or use provision and deploy command to deploy the change to Azure.

## How command-and-response works
The TeamsFx Command-Response Bots are created using the [Bot Framework SDK](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0). The Bot Framework SDK provides [built-in message handler](https://docs.microsoft.com/en-us/microsoftteams/platform/bots/bot-basics?tabs=javascript#teams-activity-handlers) to handle the incoming message activity, which requires learning curve to understand the concept of Bot Framework (e.g. the [event-driven conversation model](https://docs.microsoft.com/en-us/azure/bot-service/bot-activity-handler-concept?view=azure-bot-service-4.0&tabs=javascript)). To simplify the development, the TeamsFx SDK provides command-response abstraction layer to let developers only focus on the development of business logic to handle the command request without learning the Bot Framework SDK.

Behind the scenes, the TeamsFx SDK leverages [Bot Framework Middleware](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-middleware?view=azure-bot-service-4.0) to handle the integration with the underlying activity handlers. This middleware handles the incoming message activity and invokes the corresponding `handlerCommandReceived` function if the received message text matches the command pattern provided in a `TeamsFxBotCommandHandler` instance. After processing, the middleware will call `context.sendActivity` to send the command response returned from the `handlerCommandReceived` function to the user.

## Frequently Asked Questions

### How to build command response using adaptive card with dynamic content?
Adaptive card provides [Template Language](https://docs.microsoft.com/en-us/adaptive-cards/templating/) to allow users to render dynamic content with the same layout (the template). For example, use the adaptive card to render a list of items (todo items, assigned bugs, etc) that could varies according to different user. 

1. Add your adaptive card template JSON file under `bot/adativeCards` folder
1. Import the card template to you code file where your command handler exists (e.g. `myCommandHandler.ts`)
1. Model your card data
1. Use `MessageBuilder.attachAdaptiveCard` to render the template with dynamic card data

### How can I extend my notification bot to support command and response?
TODO

### How can I extend my command and response bot to support notification?
TODO