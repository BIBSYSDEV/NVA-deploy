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
  
  ---
**Current modules in NVA API and their deployment**

|API path|GIT repository|SAR App Name|Version|Notes|
|-----|-----|-----|-----|-----|
|/alma|nva-alma-proxy|SruLastPublication|0.1.2| |
|/upload|nva-upload-multipart|UploadMultipart|0.1.6| |
|/person|nva-bare-proxy|PersonData|0.1.3| |
|/publication|nva-publication-api|NvaPublicationApi|0.1.11| |
|/channel|nva-channel-registry|PublicationChannelRegister|0.1.2| |
|/doi-fetch|nva-fetch-doi|nva-fetch-doi|0.1.7| |
|/doi-requests|nva-doi-requests-api|NvaDoiRequestsApi|0.1.0| |
|/project|nva-cristin-projects|Projects|0.1.3| |
|/institution|nva-institution-proxy|NvaInstitutionProxy|0.1.3| |
|/download|nva-download-file|NvaDownloadPublicationFileApi|0.1.4| |
|/customer|nva-customer-api|NvaCustomerApi|0.1.3|2020-09-09| |
|/users-roles|nva-user-access-service|NvaUsersAndRolesService|0.1.2|2020-09-09| |
| |nva-cognito-post-authentication-trigger|NvaCognitoPostAuthenticationTrigger|0.1.1|2020-09-09| |
