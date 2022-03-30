# Getting-Started
A command and response bot is an app that responds to simple commands sent in Teams chat and replies a result in meaningful ways.

## How to create a command-response bot
In Visual Studio Code:

1. Open Teams Toolkit Select the Teams Toolkit ![teams-toolkit-sidebar-icon](https://user-images.githubusercontent.com/10163840/160794831-e0a370ce-888f-4176-bb26-f16a64b72118.png) icon in the Visual Studio Code sidebar or select `Teams: Create a new Teams app` from command palette.

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

## How to incrementally add a command-response bot
TODO.

## Take a tour of your app source code
After scaffolding or adding a command-response bot, you will find your bot's source code under `bot/` folder:
| File name | Contents |
|- | -|
|`bot/src/internal/initialize.ts`| Create the global bot adapter and initialize with Teams Frameworks|
|`bot/src/index.ts`| Server code to host the bot app and listen on `/api/messages` to process Teams activity with Bot Framework |
|`bot/src/helloworldCommandHandler.ts`| A hello world command handler to process a `helloworld` command and return an adaptive card as response |
|`bot/src/adaptiveCards/*.json`| Adaptive card JSON file used as your command response |

# Development
## How to add more command and response
1. Add command definition in manifest. You can edit the manifest template file `templates\appPackage\manifest.template.json` to include:
    * The command `title` that user type in the message compose area to trigger the command.
    * The `command` description for this command.

      ![manifest-add-command](https://user-images.githubusercontent.com/10163840/160374446-7fd164d6-63c9-47b2-9bf1-0d6a88731e8d.png)

1. Handle command in your bot
    * Add a .ts/.js file (e.g. `xxxCommandHandler.ts`) under `bot/src` to handle your bot command, and include the following boilerplate code to get-started:
    
        ```typescript
        import { Activity, TurnContext } from "botbuilder";
        import { TeamsFxBotCommandHandler, MessageBuilder } from "@microsoft/teamsfx";

        export class SampleCommandHandler implements TeamsFxBotCommandHandler {
            commandNameOrPattern: string | RegExp = "your-command-name";

            async handleCommandReceived(
                context: TurnContext,
                receivedText: string
            ): Promise<string | Partial<Activity>> {
                
                // TODO-0 (optional): Verify the command arguments which are received from the client if needed.
                
                // TODO-1: handle the command with your own business logic.
                
                // TODO-2: return an adaptive card or a text message. You can leverage `MessageBuilder` utilities
                // from the `@microsoft/teamsfx` SDK to facilitate building message with cards supported in Teams
                // See https://docs.microsoft.com/en-us/microsoftteams/platform/task-modules-and-cards/cards/cards-reference for more details.
            }
        }
        ```

    * Provide the `commandNameOrPattern` that can trigger this command handler. Usally it's the command name defined in your manifest, or you can use RegExp to handle a complex command (e.g. with some options in the command message)

    * Implement `handleCommandReceived` to handle the command and return a response that will be used to nofity the end users.
  

1. Register your command handler to the underlying bot.
Open `bot\src\internal\initialize.ts`, update the call to `ConversationBot.initialze` API to include your new added command handlers.

    ```typescript
    ConversationBot.initialize(adapter, { 
        commandHandlers: [
            new HelloWorldCommandHandler(),
            new xxxCommandHandler()],
    });
    ```

Now, you are all done with the code development of adding a new command and response into your bot app. You can just press `F5` to loca debug with the command-response bot, or use provision and deploy command to deploy the change to Azure.

## How to extend command-response bot for notification
TODO