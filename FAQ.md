## Why I need to Provision

This section introduces different situations you need to provision your project. If you are suggested by the Toolkit to provision your project, check whether you are one of them.

1. If your project has not been successfully provisioned yet and you choose to deploy or publish it, toolkit will ask you to provision first.
2. If your project contains bot capability and has already been successfully provisioned and just got upgrade by Toolkit, the project requires re-provision of bot Azure resources. Because before upgrade, toolkit create bot with bot registry which is legacy of Azure Resource, now we recommend to use bot service instead, So the toolkit ask you to provision again before deploy or publish your project.