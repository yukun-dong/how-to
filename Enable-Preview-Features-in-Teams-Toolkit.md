Following content applies to VS Code Teams Toolkit v2.8.0+ and TeamsFx CLI v0.7.0+. You can find content for older tooling versions in page history.

> Please be advised these features are currently under active development, with a lot of changes taking place. Please expect breaking changes as we continue to iterate.
We really appreciate your feedback! If you encounter any issue or error, please report issues to us [here](https://github.com/OfficeDev/TeamsFx/issues/new/choose).

# How to enable preview features
## VS Code Teams Toolkit
1. Upgrade to the latest [Teams Toolkit](https://marketplace.visualstudio.com/items?itemName=TeamsDevApp.ms-teams-vscode-extension).
1. Open Visual Studio Code and find `Manage` icon from sidebar (Bottom Left) 
1. Select `Settings` and find `Teams Toolkit` under `Extensions` section.
1. Tick the checkbox for `Insider Preview` setting.
1. Restart Visual Studio Code.

## TeamsFx CLI
1. Upgrade to the latest [TeamsFx CLI](https://www.npmjs.com/package/@microsoft/teamsfx-cli).
1. Set `TEAMSFX_INSIDER_PREVIEW` environment variable to `true` (either globally or for current shell)

# Preview features

## Provision Azure Resources with ARM Template
Teams Toolkit provides seamless integration with Azure resources, and we integrate with ARM so that you can now declaratively provision Azure resources your application needs using infrastructure as code approach using [Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview) as a domain-specific language  (DSL) to author [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/overview) template.

> Please note following support is still in progress now, please stay tuned. 
> * Add APIM

If you want to customize the ARM template, just update the bicep files at `templates/azure` and Azure parameter files at `.fx/configs`, then trigger provision command again to provision with your customized ARM template.
The Azure parameter file supports referencing values from environment variable. Just put `{{YOUR_ENV_NAME}}` to the value part, and our tooling will replace it with actual value. There're also some predefined placeholders in the parameter file. We're working to provide more detailed documents for you. Please stay tuned.

Happy coding!