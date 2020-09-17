# NVA-deploy
Deployment of NVA consists of 2 templates which is used to create resources in 2 separate parent stacks for each of the deployment phases; sandbox, test and production:
 * [deploy_nva_frontend.yml](deploy_nva_frontend.yml) to deploy NVA frontend GUI
 * [deploy_nva_api.yml](deploy_nva_api.yml) to deploy the lambdas / applications which forms the backend resources of NVA
 
 The templates references published and tagged versions of applications (SAM resources) published in the Serverless Application Repository (SAR).
 To make a component available in SAR it must be published to SAR, this is done via an AWS CodePipeline connected to the master branch in github. [NVA-infrastructure on github](https://github.com/BIBSYSDEV/NVA-infrastructure) contains descriptions and templates to set up a CodePipeline to publish a component to SAR
 When the resource is build and published it is ready to be deployed.
 
  * if the resource is already deployed; update version in the template
  * if it is a new resource, copy the SAM resource from available appications in SAR
  * if the resource is to be undeployed, remove it from the template
  
  Update the parent stack with the template. Done!
  
***
  
  **deploy_nva_api.yml** template parameters. 
  
  |Parameter|Description|Default value|
  |-----|-----|-----|
  | ApiDomainCertificate | ARN  of certificate to assign to custom-domain | {{secretsmanager:ApiDomainCertificate}} |
  | ApiDomainName | What is the custom-domain for backend API | api.nva.unit.no |
  | BareApiKey | Key to access bare | {{secretsmanager:bareApiKey}}|
  | BareHost | Address to bare | {{secretsmanager:BareHost}} | 
  | AlmaSruHost | Address to Alma-SRU | {{secretsmanager:AlmaSruHost}} |
  
  **deploy_nva_frontend.yml** template parameters
  
  |Parameter|Description|Default value| 
  |-----|-----|-----|
  | ApplicationDomainCertificateArn | ARN  of certificate to assign to the assosiated Domain | {{secretsmanager:ApplicationDomainCertificateArn}} |
  | ApplicationDomain | Base part of the domain the stack and components will be deployed to. Must be covered by the certificate. Used to calculate the other addresses in the deployment | frontend.test.nva.aws.unit.no / nva.unit.no |
  | OrcidBaseUrl | Address to ORCID services | {{secretsmanager:OrcidBaseUrl}} |
  | OrcidClientID | Clientid for ORCID | {{secretsmanager:OrcidClientID}} | 
  | OrcidClientSecret | Secret to authenticate for ORCID | {{secretsmanager:OrcidClientSecret}} |
  | AlmaSruHost | URL to ALMA Library services | {{secretsmanager:AlmaSruHost}} |
  
***

## Deployment steps

 1. Identify repositories with changes.
 2. Merge develop into master for each repository with changes.
 3. Create new Github release in repository.
 4. Verify CodePipeline for publishing to SAR (Serverless Repo) has run successfully by checking versions and and date in SAR.
 5. Update deployment templates with new applications, versions and paramters (if any), see above.
 6. Verify that any external resources have been updated (DynamoDB, Secrets, Elasticsearch, Cognito etc).
 7. Deploy templates by updating stacks in Cloudformation (from local file but ensure that branch is up to date).
 8. Verify changes to stack is Completed.
 9. Deployment is done.

## NVA Applications Overview

Overview of Serverless Applications in the NVA platform.

### Frontend

**[Frontend](https://github.com/BIBSYSDEV/NVA-frontend)**

Frontend for the NVA application.

### APIs

These applications use DynamoDB tables. Ensure all tables and indices are up to date before updating
application itself.

**[Publication API](https://github.com/BIBSYSDEV/nva-publication-api)**

For managing Publications.

**[Customers API](https://github.com/BIBSYSDEV/nva-customer-api)**

For managing Customer Organizations.

**[DOI Requests API](https://github.com/BIBSYSDEV/nva-doi-requests-api)**

For executing DOI Requests and metadata updates to DOI provider API (Datacite).

**[Users and Roles Service](https://github.com/BIBSYSDEV/nva-user-access-service)**

For managing users of NVA and their roles. Uses API Key stored as Secret in AWS to protect parts of 
the API.

**[Upload Multipart](https://github.com/BIBSYSDEV/nva-upload-multipart)**

For uploading files associated with Publications.

**[Download Publication File API](https://github.com/BIBSYSDEV/nva-download-file)**

For downloading files associated with Publications.

### Triggers

**[Cognito Post Authentication Trigger](https://github.com/BIBSYSDEV/nva-cognito-post-authentication-trigger)**

For user verification and creation. Looks up existing users or creates a default user for new logins from Customer Organizations. Uses API Key 
stored as Secret in AWS to communicate with Users and Role Service.

### Proxies to external services

**[Fetch DOI](https://github.com/BIBSYSDEV/nva-fetch-doi)**

Lookup, transformation and persistence of data provided by external DOI metadata services (Datacite, Crossref).

**[SRU Last Publication](https://github.com/BIBSYSDEV/nva-alma-proxy)**

Lookup most recent publication in library system based on name string, returns Authority identifier,
 title and date. Accessed during first login to associate User with existing Identities in ARP.

**[Person Data](https://github.com/BIBSYSDEV/nva-bare-proxy)**

For looking up metadata for Person from ARP (Authority Register for Personas).

**[Publication Channel Register](https://github.com/BIBSYSDEV/nva-channel-registry)**

For looking up Publication Channels in NSD DBH.

**[Projects](https://github.com/BIBSYSDEV/nva-cristin-projects)**

For looking up Projects in Cristin.

**[Institution Proxy](https://github.com/BIBSYSDEV/nva-instituion-proxy)**

For viewing structured information about Organisations in Cristin? Basically, this service provides
 a top-level list of Norwegian research Organisations and provides structured hierarchical views of departments in these.

## Deployment considerations

**If a DynamoDB table external to the template has changed**

Update the table using the CloudFormation template (do this) or apply the changes manually (don't do this). Make sure to backup any existing data first.