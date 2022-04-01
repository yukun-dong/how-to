# Notification in Teams

Notification in Teams means you can proactively message an individual person, or a chat, or a channel via plain text or massive card types.

Microsoft Teams Framework (TeamsFx) supports two majoy ways to create a Notification project, [Teams Bot App](#teams-bot-app) and [Teams Incoming Webhook](#teams-incoming-webhook).

Here's the comparison of the two approaches to help you make decision.

| | **Teams Bot App** | **Teams Incoming Webhook** |
| - | - | - |
| Able to message individual person | Yes | No |
| Able to message group chat | Yes | No |
| Able to message channel | Yes | No |
| Able to send card message | Yes | Yes |
| Able to send welcome message | Yes | No |
| Able to retrieve Teams context | Yes | No |
| Require installation step on Teams | Yes | No |
| Require Azure resource | Azure Bot Service | No |

## Teams Bot App

To create a Teams Bot Notification App, see [Create a new Notification Project](%5BDocument%5D-Notification-(Preview-feature)#create-a-new-notification-project#create-a-new-notification-project).

The project is based on Microsoft Bot Framework and will require Azure Bot Service. TeamsFx helps you on creating project, local debugging, provisioning and deploying Azure resources, managing and previewing your Teams app.

## Teams Incoming Webhook
// TODO