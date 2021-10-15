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

## Managing Multiple Environments in Teams Toolkit
 The Teams toolkit provide a simple way for developers to easily create multiple environments and deploy artifacts to a target environment for a Teams app.

### Create a New Environment Copy
By default, the toolkit generates a `dev` environment after creating a new project. To add another environment for the app, you can use the `Teams: Create new environment copy` command in the tree view:

![add-env](https://user-images.githubusercontent.com/10163840/137431115-3a68fde9-ee81-49a3-a100-c5cc13f51bc2.png)

And then input your new environment name:
![input-env](https://user-images.githubusercontent.com/10163840/137431272-253cb636-18cf-4292-991e-f61905d2c4cc.png)

If you have more than one existing environments, you will need to select an existing environment to create the environment copy. Behind the scenes, the file content of `config.<newEnv>.json` and `azure.parameters.<newEnv>.json` will be copied from the existing environment you selected.

### Customize Environment Provision
The toolkit provides the flexibility to let users customize the resource provision behavior for an environment.

Here is some common scenarios we can support for customized provision:
| Scenarios | Where to customize |
|--|--|
| Azure Resource Customization | <ul> <li>bicep files under `templates/azure`.</li> <li>`.fx/azure.parameters.<envName>.json`.</li></ul> |
| App Manifest | <ul> <li>`templates/manifest.template.json`.</li> <li>`manifest` section in`.fx/config.<envName>.json`.</li>  </ul> |
| Reusing existing AAD app for Teams app | <ul> <li>`auth` section in`.fx/config.<envName>.json`.</li> </ul> |
| Reusing existing AAD app for bot | <ul> <li>`bot` section in`.fx/config.<envName>.json`.</li> </ul> |
| Skip add user when provision SQL | <ul> <li>`skipAddingSqlUser` property in`.fx/config.<envName>.json`.</li> </ul> |

- For how to use the environment config file, you can refer to the [environment configuration schema](https://aka.ms/teamsfx-config).
- For how to use BICEP files in Teams toolkit, you can refer to [official BICEP document](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/) for help.

### Select Target Environment on Provision/Deploy/Publish/Preview
As we introduce multi-environments concept in Teams toolkit, for all environment related operation, you will need to select the target environment to perform with.

![select-env](https://user-images.githubusercontent.com/10163840/137431518-31c5fa2d-7867-441b-ab90-c5f87f78e8b9.png)

### Appendix
Main changes in project's folder structure:
- `.fx/configs`: config files that user can customize for the Teams app.
    - `config.<envName>.json`: per-environment configuration file.
    - `azure.parameters.<envName>.json`: per-environment parameters file for Azure BICEP provision.
    - `projectSettings.json`: global project settings which apply to all environments.
    - `localSettings.json`: local debug configuration file.
- `.fx/publishProfiles`: provision result that generated by the Toolkit.
- `templates`
    - `appPackage`: app manifest template files.
    - `azure`: BICEP template files. 


Happy coding!