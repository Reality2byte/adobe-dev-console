# Migrating from Service Account (JWT) credential to OAuth Server-to-Server credential


As of June 30, 2025, Service Account (JWT) credentials have reached their end of life and are no longer supported. All server-to-server integrations must use the OAuth Server-to-Server credentials.

The new OAuth Server-to-Server credentials simplify all aspects of application development - experimentation, implementation, and maintenance. See the section on [Why OAuth Server-to-Server credentials?](#why-oauth-server-to-server-credentials) below to learn more.

Service Account (JWT) credentials will not work after current certificates expire or March 1, 2026, whichever is earlier. Therefore, you must migrate your integration to use the new OAuth Server-to-Server credential ASAP.

Migrating your applications to the OAuth Server-to-Server credential is a simple two-step process that enables a zero downtime migration for your applications and integrations. Please read our [migration guide](#migration-overview) below to familiarize yourself with the migration process.

If you have more questions, check out our [frequently asked questions](#faqs) or visit the Adobe Developer Console [forums](https://experienceleaguecommunities.adobe.com/t5/adobe-developer-console/ct-p/adobe-io-console).

## Table of Contents

+ [Why OAuth Server-to-Server credentials?](#why-oauth-server-to-server-credentials)
+ [Deperecation Timelines](#deprecation-timelines)
+ [Migration Overview](#migration-overview)
   + [Preparation: Find out which applications are using the Service Account (JWT) credentials](#preparation-find-out-which-applications-are-using-the-service-account-jwt-credentials)
   + [Step 1: Add the new credential to your project](#step-1-add-the-new-credential-to-your-project)
   + [Step 2: Update your application and delete old credential](#step-2-update-your-application-and-delete-old-credential)
     + [Update your application](#update-your-application)
     + [Test if the new credential is working](#test-if-the-new-credential-is-working)
     + [Delete the old credential](#delete-the-old-credential)
   + [Cancelling Migration](#cancelling-migration)


## Why OAuth Server-to-Server credentials?

The new OAuth Server-to-Server credentials rely on the OAuth 2.0 client credentials grant. Therefore, you can use industry-standard [OAuth 2.0 libraries](./implementation.md#generating-access-tokens-using-standard-oauth2-libraries) to implement access token generation in your application.

Unlike the Service Account (JWT) credential, the new credential does not use a public certificate and private key pair to generate access tokens. As an application developer, you do not have to periodically rotate the public certificates and private key pairs when they expire. Also, the credential setup process is greatly simplified, and you do not have to download and save the private key on your machine. 

Lastly, while the new OAuth Server-to-Server credentials do not use expiring certificates, they still allow client secret rotation through the UI and API. Read more about [rotating secrets programmatically](./implementation.md#rotating-client-secrets-programmatically) in our guide on [rotating client secrets](./implementation.md#rotating-client-secrets).


|                                                      | OAuth Server-to-Server credential                                                                                         | Service Account (JWT) credential                                                                                   |
|------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| Do expiring certificates need to be replaced every year? | This credential does not use public certificates or private key pairs. You are not forced to rotate certificates every year. | You need to rotate the public certificate and private key pair every year. |
| Generating access tokens for experimentation         | You can generate an access token for quick experimentation with a [one-click button](./implementation.md#generate-access-tokens) on the Developer Console.              | Generating access tokens is a 3-step process spanning the Developer Console UI and your command-line terminal. |
| Generating access tokens in your application         | You can use [standard OAuth 2.0 libraries](./implementation.md#generating-access-tokens-using-standard-oauth2-libraries) to generate access tokens in your application programmatically.| You can implement access token generation programmatically by reading our documentation. |
| Rotating client secrets programmatically             | You can rotate client secrets on-demand through the Dev Console UI and API. [Learn more](./implementation.md#rotating-client-secrets-programmatically).| You can rotate the public certificate and private key pair manually through the UI. |

## Deprecation Timelines

1. **May 1, 2023**
   * Adobe announces the deprecation of Service Account (JWT) credentials. The deprecation timelines and the end-of-life date are also announced. 
   * All existing integrations using Service Account (JWT) credentials continue to work.

2. **June 3, 2024** 
   * After June 3, 2024, you cannot create new Service Account (JWT) credentials.
   * All existing integrations using Service Account (JWT) credentials continue to work.

3. **June 30, 2025 (End of Life)**
   * After June 30, 2025, you cannot refresh certificates for integrations using Service Account (JWT) credentials. Your integrations will stop working once existing certificates expire.
   * Adobe will automatically convert Service Account (JWT) credentials to OAuth Server-to-Server credentials after certificates expire or on March 1, 2026 (whichever is earlier), breaking any integration still relying on the old credentials.


## Migration Overview

The migration to the OAuth Server-to-Server credential is a simple process that enables a zero downtime migration for your application. You can follow the steps below to migrate your application.

### Preparation: Find out which applications are using the Service Account (JWT) credentials

To find out which of your Adobe applications and integrations are using the Service Account (JWT) credential

1. Log in to the Adobe Developer Console and open the [Projects Tab](https://developer.adobe.com/console/projects).

2. On the filtering menu on the left side, select the filter `Attention Required` > `Has Service Account (JWT) credential`. This will show you all projects with a Service Account (JWT) credential.

3. Check out the project descriptions set by your team members to understand which application is using a given project. To identify project maintainers in your organization, you can check who created or last modified that project or the project's activity logs.

Perform the steps below for each application/project you have identified. If a project is not in use, we recommend deleting the project or the Service Account (JWT) credential in the project.

Note: you do not have to worry about auto-generated projects. Adobe will automatically migrate all auto-generated projects before June 30, 2025.

### Step 1: Add the new credential to your project

Once you have identified the application that needs to be updated and its related project on the Developer Console, the first step is to add the new credential to your project to begin using the new credential to generate access tokens and update your application.

<InlineAlert slots="text"/>

Adding an equivalent OAuth Server-to-Server credential will not impact your running application. Your running application can still use the Service Account (JWT) credential to generate access tokens.

1. Log in to the Developer Console and open your project. Open the Service Account (JWT) credential tab from the left side nav and view the migration card.

2. On the migration card, click the button to add an equivalent OAuth Server-to-Server credential. Adding an OAuth Server-to-Server credential to your project will start the migration. Note: during the migration, you cannot add any API or service to your project. Some finer points about the added credential -

   * The new OAuth Server-to-Server credential has the same client id, technical account, connected APIs and services, scopes, and product profiles as the old Service Account (JWT) credential.

   * A token generated by the new credential will be identical to one generated by the old credential and have identical access.

   * The difference between the two credentials is in the token generation mechanism and the use of public certificates and private key pairs.

   * At this point, you can use either credential to generate access tokens. This ensures that your running application can continue generating access tokens using the Service Account (JWT).

   * Meanwhile, you can test the new credential by generating access tokens using the Developer Console UI or a cURL command. We also recommend reading our [implementation guide](./implementation.md) that points to several [standard OAuth 2.0 libraries](./implementation.md#generating-access-tokens-using-standard-oauth2-libraries) to generate access tokens programmatically.

### Step 2: Update your application and delete old credential

<InlineAlert slots="text"/>

If you are migrating Service Account (JWT) credentials used in AEM, please see the [migration guide for AEM customers](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/security/jwt-credentials-deprecation-in-adobe-developer-console).

#### Update your application

1. Update your application code to use the new credential to generate access tokens.
   
   * At this point, you can make code changes to your application and deploy it. Because both your credentials work and you can use either to generate access tokens, you can deploy your updated application to test, staging, and even production environments.

#### Test if the new credential is working 

1. Once you have updated your application and deployed it to production, the next step is to review whether your application has stopped using the old credential completely. And if so, delete the old credential to complete the migration.

2. Log in to the Developer Console and navigate to your project. Open the Service Account (JWT) credential tab from the left side nav and view the migration card.

3. On the migration card, click the button to review timestamps and delete the old credential. This will launch a dialog that walks you through your application's timestamp and credential usage. Based on the status of the two checks (listed below), the UI displays will prompt you to the recommended action.

   * Did your application generate access tokens using the new credential more recently than the old credential?

   * Has it been at least 24 hours since the last time an access token was generated using your old credential?
  
4. If all is well, you can proceed to the final action of deleting the old credential. 

#### Delete the old credential

<InlineAlert slots="text"/>

Completion of this step on the Developer Console will complete the migration, and the step cannot be rolled back. We recommend that developers pay attention to the prompts on the UI and exercise care when performing this step.
   
1. Once you are sure that you have replaced the old credential successfully, the dialog will take you to the last step of deleting the old credential.

   * To confirm this step, you must type in the project name to confirm your intentions.

   * Note: once the old credential is deleted, the step cannot be rolled back. We recommend that developers pay attention to the prompts on the UI and exercise care when performing this step.

### Cancelling Migration

During the migration, you cannot add any APIs or other services to the project. Furthermore, during migration, you cannot rotate client secrets for the OAuth Server-to-Server credential.

If you need to add other services or disable the OAuth Server to Server credential in case of a client secret leak - you can cancel the migration.

Canceling the migration will delete the OAuth Server-to-Server credential from your project. If your application already uses the new credential, it will stop working once you cancel the migration.
