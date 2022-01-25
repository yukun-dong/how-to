# TeamsFx Roadmap 2022

TeamsFx roadmap typically covers a calendar year. It is not built from scratch but will base on the existing features.

The roadmap is for information only. The listed item may be added, removed, or changed due to priority change, dependency readiness and customer learnings, demands, etc.

## Mission
enable every developer and their team to achieve more with Microsoft 365.
### Platform Vision
Microsoft 365 is the best platform for building work apps. Apps are better in M365, easier for anyone to build, and trusted by organizations.
### Tooling Vision
Teams Toolkit makes it fast and easy for pro devs and fusion devs to build trusted apps for Microsoft 365. 

## TeamsFx 2022 Objectives
1. Accelerate developers' time to market
2. Tooling builds high quality apps that end users value
3. Developers prefer our tooling when integrating M365

We are planning Teams Toolkit GA by mid-2022, below the proposed features are grouped by Themes.

## Key Themes
### Get to the aha! moment fast
* Instant Tenant Procurement integration with Toolkit, to easily acquire Dev/test M3675 tenant
* Teams Toolkit for VS 2022
* integrate with VS native identity providers
* Local cert installation for WSL developers
* Improved docs on doc site for different purpose
* Easily consumed sample codes by scenarios
* Walkthrough guides on Toolkit

***

* Use Teams Toolkit with GitHub CodeSpaces
* CLI feature parity with VS for .NET/Blazor projects
### Enable incremental Teams app development
* Bring TeamsFx to existing projects
* SPFx support enhancement
* Graph Toolkit integration
* Bring a .NET web app to a Teams tab
* Add Teams capabilities to a .NET web app

***

* Support Office add-ins integration with Toolkit and SDK.
### Native Teams communication experience development
* Support messaging extensions from VS Teams Toolkit 
* Support bot from VS Teams Toolkit
* Support search extension
* Improve in-meeting app experience
* Simplify bot creation experience
* Scaffold Teams app without SSO upfront.
### Build best-in-class integrations for Azure
* Graph connector support
* Dev can add any number of instances of Azure resources of the same type
* Dev can add any number of instances of app type/capabilities allowed by manifest
* Dev can authenticate user without simple auth server in frontend workload
### Build apps for Teams fast
* Improve inner loop performance
* Enhance support for Outlook and Office hubs

***

* Mobile debugging support
* Replace Ngrok with Basis
### Make Teams app development enterprise ready
* Allow collaborating when developing on the same project
* Secure customer secrets using KeyVault
* Use Service Principal login for Azure account in CI/CD
* Devs can easily clean up all TeamsFx created resources and artifacts
Customize AAD app in a config driven approach
* Scaffolds CI/CD templates
* Enable multiple remote environments

***

* Automate the approval process for applications in Teams Admin portal
### Improving the tooling fundamentals
* Improve errors
* Improve logging information
* Security and privacy hardening
* Accessibility, localization improvements

## Beyond GA
### UI tooling for app UI design
When designing an app, offer needed tooling support and integration for leveraging Fluent UI, Storybook based UI component library, Adaptive Card designer, etc.
### Tooling integration for testing apps
Leverage playwright for app testing
### More Azure integrations
Key services: APIM, Logic App, Static Web App...
### More complaint apps (trusted by default)
offer compliance help throughout app development lifecycle, to accelerate the process to get compliance.
### Unified data access
Offer a unified data access layer that's consumable through GraphQL for any Teams app.