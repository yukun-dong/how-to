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

## Troubleshooting:
### Error message "Permission (scope or role) cannot be deleted or updated unless disabled first
This usually caused by `AAD_APP_ACCESS_AS_USER_PERMISSION_ID` environment variable value changed.

To fix this error: find the id of `access_as_user` scope for your application and set it to `AAD_APP_ACCESS_AS_USER_PERMISSION_ID` environment variable in `.env.{env_name}`.