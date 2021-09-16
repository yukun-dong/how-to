# File structure changes after migration
Once migration success, the file structure has changed. </p>
There generates new folder named **templates** under root path, 
new folders named **configs**, **migrationbackup**, **publishProfiles** under .fx folder
# Backup
We suggest you backup the project before migration.</p>
Toolkit helps to backup env.default.json file under .fx/migrationbackup 
# Must-do steps after migration success
After migration, you must provision again. </p>
Toolkit will creates some new resources for bot / tab instead of reusing existing resources 
# Provision issues after migration
Provision may fail if project use Azure SQL resource, you may modify *templates/azure/modules/azureSqlProvision.bicep* manually and run provision again </p>
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


