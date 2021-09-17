As Teams Toolkit continues to evolve, some of the features will require an update to your project code structures such as provisioning Azure resources with ARM and creating multiple environments. Teams Toolkit will automatically upgrade your original project to support Multi-Env and ARM features.

## How to enable ARM and Multi-Env feature flag
`ARM`: [Enable ARM feature Gate](https://github.com/OfficeDev/TeamsFx/wiki/Enable-Preview-Features-in-Teams-Toolkit#how-to-enable-this-feature) </p>
`Multi-Env`: set environment variable `TEAMSFX_MULTI_ENV` to `true`.

## File structure change
Once migration succeeds, your project file structure will be changed.
We will generate a new folder named `templates` under root path, and `configs`, `migrationbackup`, `publishProfiles` under `.fx` folder.

All other original files will be deleted except `subscriptioninfo.json` under `.fx` folder.

## Backup your project
We recommend you initialize your project with `git` or backup the project before migration to better tracking file changes. Teams Toolkit will automatically backup `.fx/env.default.json` file to `.fx/migrationbackup`.

## Required Steps After Migration
After migration success, you will need to provision your project again. And please note that Teams Toolkit will create new Azure resources for to host your bot and tab project instead of reusing existing resources.

## Known Issues
Provision again may fail after migration success.
1.  Azure SQL failure. please modify `templates/azure/modules/azureSqlProvision.bicep` manually and run provision again </p>
```
resource sqlServer 'Microsoft.Sql/servers@2021-02-01-preview' = {
  location: resourceGroup().location
  name: sqlServerName
  properties: {
    administratorLogin: empty(administratorLogin) ? null: administratorLogin
    administratorLoginPassword: administratorLoginPassword
  }
}
```


