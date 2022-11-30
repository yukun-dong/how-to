> The content in this page is under construction and may change in the future.

The page describes the available actions that can be used in `app.yml` in Teams Toolkit.

# aadApp/create
This action will create a new AAD app for you if AAD_APP_CLIENT_ID environment variable is empty.

## Syntax:
```
  - uses: aadApp/create
    with:
      name: <your-application-name> # Required. The name of AAD application. Note: when you run configure/aadApp, the AAD app name will be updated based on the definition of manifest. If you don't want to change the name, ensure the name in AAD manifest is same with the name defined here.
      generateClientSecret: true # Required. If the value is false, the action will not generate client secret for you
```

## Output:
* AAD_APP_CLIENT_ID: the client id of AAD app
* AAD_APP_CLIENT_SECRET: the client secret of AAD app
* AAD_APP_OBJECT_ID: the object id of AAD app
* AAD_APP_TENANT_ID: the tenant id of AAD app
* AAD_APP_OAUTH_AUTHORITY_HOST: the host of OAUTH authority of AAD app
* AAD_APP_OAUTH_AUTHORITY: the OAUTH authority of AAD app

# aadApp/update
This action will update your AAD app based on give AAD app manifest. It will refer the `id` property in AAD app manifest to determine which AAD app to update.

## Syntax:
```
  - uses: aadApp/update
    with:
      manifestTemplatePath: ./aad.manifest.template.json # Required. Relative path to teamsfx folder. Environment variables in manifest will be replaced before apply to AAD app.
      outputFilePath : ./build/aad.manifest.${{TEAMSFX_ENV}}.json # Required. Relative path to teamsfx folder. This action will output the final AAD manifest used to update AAD app to this path.
```

## Output:
* AAD_APP_ACCESS_AS_USER_PERMISSION_ID: the id of access_as_user permission which is used to enable SSO

# npx/command
This action will execute `npx` commands under specified directory with parameters. It can be used to run `gulp` commands to bundle and package sppkg.

## Syntax:
```
  - uses: npx/command
    with:
      workingDirectory: ./src
      args: gulp bundle --ship --no-color
  - uses: npx/command
    with:
      workingDirectory: ./src
      args: gulp package-solution --ship --no-color
```

## Output:
* A client-side solution package that is located in `{workingDirectory}`/sharepoint/solution/*.sppkg

# spfx/deploy
This action will upload and deploy generated sppkg to SharePoint app catalog. You can create tenant app catalog manually or by setting `createAppCatalogIfNotExist` to true if you don't have one in current M365 tenant.

## Syntax:
```
  - uses: spfx/deploy
    with:
      createAppCatalogIfNotExist: false # If the value is true, this action will create tenant app catalog first if not exist, default value is `false`.
      packageSolutionPath: ./src/config/package-solution.json # Required. Path to package-solution.json in SPFx project. This action will honor the configuration to get target sppkg.
```

## Output:
NA

# teamsApp/copyAppPackageForSPFx
This action will copy the Teams App zipped package to `teams` folder in SPFx directory to keep it updated. This is to ensure user will have aligned experience whether to publish Teams App from Teams Toolkit or manually sync to Teams in SharePoint app catalog.

## Syntax:
```
  - uses: teamsApp/copyAppPackageForSPFx
    with:
      appPackagePath: ${{TEAMS_APP_PACKAGE_PATH}}
      spfxFolder: ./src # Path to SPFx solution.
```

## Output:
NA

## Troubleshooting:
### Error message "Permission (scope or role) cannot be deleted or updated unless disabled first
This is a known issue that OAuth permission id for an existing permission in your AAD manifest is different than the id in AAD application. One possible reason is the value of `AAD_APP_ACCESS_AS_USER_PERMISSION_ID` environment variable in `.env.{env_name}` is out of sync.

To fix this error: find the id of `access_as_user` scope for your application in [AAD app registration portal](https://ms.portal.azure.com/#view/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) and set it to `AAD_APP_ACCESS_AS_USER_PERMISSION_ID` environment variable in `.env.{env_name}`.

![image](https://user-images.githubusercontent.com/16605901/204182487-8eb46f6d-cee6-4d97-9cd4-68db59d4a572.png)

# appsettings/generate
This action will override or add environment viriables to target file (e.g., appsettings.Development.json)

## Syntax:
```
  - uses: appsettings/generate
    with:
      target: ./appsettings.Development.json # Required. The relative path of project configuration file
      appsettings: # Required.
        BOT_ID: ${{BOT_ID}}
        BOT_PASSWORD: ${{SECRET_BOT_PASSWORD}}
```

## Output:
NA