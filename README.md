# Azure Static Web Apps + NextJS + Azure Pipelines
Building and deploying a Nextjs site on Azure Static Web Apps with Azure Pipelines

## About Azure Static Web Apps

- Azure's answer to Vercel, Netlify, AWS Amplify etc
- Like an enhanced static file host
- NextJS deployments can run in two modes
  - Static HTML export - similar to hosting from an S3 bucket
  - Hybrid - similar to Vercel, Netlify etc
- Includes internal build tool Oryx
- Can deploy to multiple environments inside a single web app

The killer feature of static web apps is [Hybrid websites](https://learn.microsoft.com/en-us/azure/static-web-apps/deploy-nextjs-hybrid) which allows you to use NextJS SSR and API endpoints.