## Collaborating on TeamsFx Project
Previous version of Teams Toolkit is not easy for multiple users to develop the same project due to missing privilege to access Teams APP and AAD APP. If multiple developers want to share remote resources and work together, they need to manually handle permissions of Teams App and AAD APP which need deep understanding the low-level details about the TeamsFx project.

Teams Toolkit now natively support add other collaborators for TeamsFx project which is much easy and straightforward for collaborative development.

### Collaborating - Use VSCode

#### Usage

- To use collaboration feature, you need to **login M365 and Azure account**(Only needed for Tab project, and for SPFX project, it is not needed) and your **TeamsFx project should be provisioned first**

- In the Teams Toolkit panel, click ENVIRONMENT, and expand an environment name which you want to work with others, then you can see collaboration buttons:

  ![collaboration-buttons](https://user-images.githubusercontent.com/5545529/168954079-72d3e24d-a862-4067-8df9-f2539cedae3c.png)


- Click grant permission button on the left, then you can add another M365 account email address as collaborator (this account should be in the same tenant with your M365 account):

  ![input-collaborator-email](https://user-images.githubusercontent.com/5545529/168954580-d4012f4d-f99e-4c97-92af-ffe68e7ecb6b.png)


- Click List permission button on the right to view all collaborators for the current environment:
  ![view-collaborators](https://user-images.githubusercontent.com/5545529/168957068-84ff633e-8612-4805-a1a2-fe4afe42a00d.png)


- Share your project with the collaborator:
  - Commit your project to GitHub repository
  - Collaborator clone the project to his computer
  - If your project contains Bot capability, share secret file `.fx\states\[Environment-Name].userdata` to the collaborator, and collaborator should copy the secret file to same place in the project:
    ![secret-file](https://user-images.githubusercontent.com/5545529/169196511-9a426695-af8e-4b03-980c-5fcb7935d163.png)


- Collaborator login to M365 account

- For Tab project, login Azure account which **at least has contributor permission** for all the Azure resources. Project owner can refer this [link](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal?tabs=current) to assign Azure roles using the Azure portal.

- For SPFX project, project owner needs to manually setup access policy via SharePoint admin center, please refer to this [link](https://docs.microsoft.com/en-us/sharepoint/manage-site-collection-administrators) for more details.

- Now collaborator can develop, provision and deploy the project

### Collaborating - Use CLI
Teams Toolkit CLI provides `teamsFx permission` Commands for collaboration scenario.

#### Commands
| `teamsFx permission` Commands | Descriptions |
|:------------------------------|-------------|
| `teamsfx permission grant` | Grant permission for collaborator's M365 account for the project |
| `teamsfx permission status` | Show permission status for the project | 

***

#### Parameters for `teamsfx permission grant`
- ##### `--env`
	**(Required)** Provide env name.

- ##### `--email`
	**(Required)** Provide collaborator's M365 email address. Note that the collaborators's account should be in the same tenant with creator.

#### Parameters for `teamsfx permission status`
- ##### `--env`
	**(Required)** Provide env name.

- ##### `--list-all-collaborators`
	With this flag, Teams Toolkit CLI will print out all collaborators for this project.

### Examples
Here are some examples for you to better handling permission for `TeamsFx` projects.

#### Grant Permission
```bash
teamsfx permission grant --env dev --email user-email@user-tenant.com
```

#### Show current user Permission Status
```bash
teamsfx permission status --env dev
```

#### List All Collaborators
```bash
teamsfx permission status --env dev --list-all-collaborators
```

### Limitations of collaboration feature
- Azure related permissions should be handled manually by the Azure subscription administrator via Azure portal, different Azure account should at least have contributor role for the subscription so that developers can work together to provision and deploy TeamsFx project. For more information about how to assign Azure roles using the Azure portal, you can refer this [doc](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal?tabs=current).

- If your project is SPFX project, you need to manually setup access policy via SharePoint admin center, please refer to this [link](https://docs.microsoft.com/en-us/sharepoint/manage-site-collection-administrators) for more details.

- If the project contains Bot capability, creator of the project should share secret file `.fx\states\[Environment-Name].userdata` with the collaborator, otherwise collaborator will receive errors as below when provision: 

  ![403-error](https://user-images.githubusercontent.com/5545529/168981368-2e97b9df-0f37-4eaa-acd1-33e85492b4cb.png)

