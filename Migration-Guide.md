# Prerequisites
Once you both enable the ARM and Multi-Env feature gate, toolkit will help migration original project to support Multi-Env and ARM bicep.
### How to enable ARM and Multi-Env feature gate
`ARM`: [Enable ARM feature Gate](https://github.com/OfficeDev/TeamsFx/wiki/Enable-Preview-Features-in-Teams-Toolkit#how-to-enable-this-feature) </p>
`Multi-Env`: set environment `TEAMSFX_MULTI_ENV` as true
# File structure change
Once migration success, the file structure has changed. </p>
There generates new folder named `templates` under root path, and `configs`, `migrationbackup`, `publishProfiles` under .fx folder. </p>

Besides `subscriptioninfo.json` under .fx folder, other original files will be deleted.
# Backup
We suggest you backup the project before migration.</p>
Toolkit helps to backup `.fx/env.default.json` file to `.fx/migrationbackup`
# Must-do Steps after Migration
After migration success, you must provision again. </p>
Toolkit will creates some new resources for bot / tab instead of reusing existing resources 
# Provision Issues
Provision again may meets fail after migration success.
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


