# NVA-deploy
Deployment of NVA consists of 2 templates which is used to create resources in 2 separate parent stacks for each of the deployment phases; sandbox, test and production:
 * deploy_nva_frontend.yml to deploy NVA frontend GUI
 * deploy_nva_api.yml to deploy the lambdas / applications which forms the backend resources of NVA
 
 The templates references published and tagged versions of applications (SAM resources) published in the Serverless Application Repository (SAR).
 To make a component available in SAR i must be published to SAR, this is done via an AWS CodePipeline connected to the master branch in github. [NVA-infrastructure on githum](https://github.com/BIBSYSDEV/NVA-infrastructure) contains descriptions and templates to set up a CodePipeline to publish a component to SAR
 When the resource is build and published it is ready to be deployed.
 
  * if the resource is already deployed; update version in the template
  * if it is a new resource, copy the SAM resource from available appications in SAR
  * if the resource is to be underployed, remove it from the template
  
  Update the parent stack with the template. Done!
  