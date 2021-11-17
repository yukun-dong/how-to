Teams Toolkit continuously evolve to offer useful features for developers. Recently a group of new features are available to preview in insider features, such as provisioning Azure resources with ARM templates and managing multiple environments. These insider features will require an update to your existing project code structures. Teams Toolkit can automatically upgrade your original project to support Multiple Environments and ARM provision features.

> Please note that ARM and multiple environment features will be enabled by default in the future release of Teams Toolkit. We thrive to make Teams Toolkit compatible with existing projects but long time backward compatibility is not 100% guaranteed thus we strongly recommend you updating your project configurations to continue using the latest Teams Toolkit.

## How to upgrade
You will need to enable the insider features, and Teams Toolkit will automatically upgrade your original project folder structure. Learn [how to enable insider preview features](https://github.com/OfficeDev/TeamsFx/wiki/Enable-Preview-Features-in-Teams-Toolkit#how-to-enable-preview-features).

## File structure change
Once migration succeeds, your project file structure will be changed.
We will generate a new folder named `templates` under root path, and `configs`, `migrationbackup`, `publishProfiles` under `.fx` folder.

All other original files will be deleted except `subscriptioninfo.json` under `.fx` folder.

## Backup your project
We recommend you initialize your project with `git` or backup the project before migration to better tracking file changes. Teams Toolkit will automatically backup `.fx/env.default.json` file to `.fx/migrationbackup`.

## Required Steps After Migration
If you have already provisioned the bot service before the migration, and you want to continue to use the bot service after the migration, please provision again. We will create a new bot service for this project, and other resources will not change.

## Manual work to use existing resource instead of provision new resources.

## Known Issues
* Local Debug will create a new teams App added to the Teams Developer Portal after migration success. You can get the app id from `.fx/configs/localSettings.json` file.
* Once upgrade success, if you provision resource in a new group resource using a newly created environment, this operation will cause an error. For example, you create a new environment named as test, in order to provision successful, just delete all parameters which value has exact value in  `.fx/configs/azure.parameters.test.json` 

