# Azure Static Web Apps + NextJS + Azure Pipelines

Building and deploying a Nextjs site on Azure Static Web Apps with Azure Pipelines

## About Azure Static Web Apps

- Azure's answer to Vercel, Netlify, AWS Amplify etc
- Like an enhanced static file host
- NextJS deployments can run in two modes
  - Static HTML export - similar to hosting from an S3 bucket
  - Hybrid - similar to Vercel, Netlify etc
- Includes internal build tool [Oryx](https://github.com/microsoft/Oryx)
- Can deploy to multiple environments inside a single web app

The killer feature of static web apps is [Hybrid websites](https://learn.microsoft.com/en-us/azure/static-web-apps/deploy-nextjs-hybrid) which allows you to use NextJS SSR and API endpoints.

## SSR vs SSG

- SSR - Server Side Rendering
  - NextJS will render the page on the server and send the HTML to the browser
  - This is the default mode for NextJS
  - The page will be rendered on every request
- SSG - Static Site Generation
  - NextJS will render the page on the server and save the HTML to a file
  - The page will be rendered once and then served from the file system

Currently an Azure Static Web App Hybrid website only allows the use of the API endpoint if running in SSR mode.

## Deploying a NextJS app with Azure Pipelines

I believe (?) that in order to deploy as a Hybrid website, the builtin Azure build tool Oryx has to be used to build the NextJS site. This is because [the docs])(https://learn.microsoft.com/en-us/azure/static-web-apps/deploy-nextjs-hybrid#unsupported-features-in-preview) say `skip_app_build` and `skip_api_build` features are not supported in preview. (I may be wrong on this, though!)

To set up deployment to an Azure Static Web App with Azure Pipelines, you need to:

1. Create an Azure Static Web App in the Azure portal and click `Manage deployment token` and copy the token.
1. In your Azure Pipeline, create a new variable called `static-web-apps-deploy-token`, paste the token into the value and set it to be secret.
1. Add an `AzureStaticWebApp@0` task to deploy:

```yaml
- task: AzureStaticWebApp@0
  inputs:
    azure_static_web_apps_api_token: $(static-web-apps-deploy-token)
    app_location: '/' # set this to the folder of your NextJS app
```

### Size Error

When we tried to deploy our NextJS app to Azure Static Web Apps, we got the following error:

```
The content server has rejected the request with: BadRequest
Reason: The size of the function content was too large. The limit for this Static Web App is 104857600 bytes.
```

This is because of the NextJS build cache folder. This folder isn't used at runtime, and this GitHub issue explains how to fix the error - by adding a custom build command to remove the cache folder before deployment:

```yaml
- task: AzureStaticWebApp@0
  inputs:
    ...
    api_build_command: 'rm -rf .next/cache/'
```

### Using Web App Environments

Azure Static Web Apps allows you to deploy to multiple environments inside a single web app. This is great for seeing/testing PR or staging build outputs. To enable them, just specify the production branch (usually `main` or `master`):

```yaml
- task: AzureStaticWebApp@0
  inputs:
    ...
    production_branch: 'main'
```
