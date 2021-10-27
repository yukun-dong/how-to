As Teams Toolkit continues to evolve, some of the features will require an update to your project code structures such as provisioning Azure resources with ARM and creating multiple environments. Teams Toolkit will automatically upgrade your original project to support Multi-Env and ARM features.

> Please note that ARM and multiple environment features will be enabled by default in the future release of Teams Toolkit. We thrive to make Teams Toolkit compatible with exiting projects but long time backward compatibility is not 100% guaranteed thus we strongly recommend you updating your project configurations to continue using the latest Teams Toolkit.

## How to enable ARM and Multi-Env feature flag
Learn [how to enable insider preview features](https://github.com/OfficeDev/TeamsFx/wiki/Enable-Preview-Features-in-Teams-Toolkit#how-to-enable-preview-features).

## File structure change
Once migration succeeds, your project file structure will be changed.
We will generate a new folder named `templates` under root path, and `configs`, `migrationbackup`, `publishProfiles` under `.fx` folder.

All other original files will be deleted except `subscriptioninfo.json` under `.fx` folder.

## Backup your project
We recommend you initialize your project with `git` or backup the project before migration to better tracking file changes. Teams Toolkit will automatically backup `.fx/env.default.json` file to `.fx/migrationbackup`.

## Required Steps After Migration
If you have already provisioned the bot service before the migration, and you want to continue to use the bot service after the migration, please provision again. We will create a new bot service for this project, and other resources will not change.
## Known Issues
* Local Debug will create a new teams App added to the Teams Developer Portal after migration success. You can get the app id from **.fx/configs/localSettings.json** file.
* Once upgrade success, if you provision resource by create a new group resource in a new environment, this operation will cause an error. For example, you create a new environment named as test, then just delete all parameters which value has exact value in  `.fx/configs/azure.parameters.test.json` 

