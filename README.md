# PipelineBees

### Azure devops pipeline for app testing

This pipeline aims to run Sonar Cloud and in case of validation below the quality rule, the application will stop.
Use BrowserStack during compilation using the artifact in local environment.
Use the MAC Stadium IOS agent as an integration.

Here below is the pipeline to use Azure Devops and GithubAction.
#####################################################################################


trigger:
- main

pool:
  vmImage: 'macOS-latest'

steps:
- task: SonarCloudPrepare@1
  inputs:
    SonarCloud: 'SonarCloud Connection'
    organization: '<Your SonarCloud Organization>'
    scannerMode: 'CLI'
    configMode: 'manual'
    cliProjectKey: '<Your SonarCloud Project Key>'
    cliProjectName: '<Your SonarCloud Project Name>'
    cliSources: '.'
  displayName: 'Prepare analysis on SonarCloud'

- task: SonarCloudAnalyze@1
  displayName: 'Code Analysis'

- task: SonarCloudPublish@1
  inputs:
    pollingTimeoutSec: '300'
  displayName: 'Publish Quality Gate Result'

- task: BrowserStack@1
  inputs:
    credentialsType: 'ServiceEndpoint'
    browserStackServiceEndpoint: '<Your BrowserStack Service Endpoint>'
    browserStackUserName: '<Your BrowserStack Username>'
    browserStackAccessKey: '<Your BrowserStack Access Key>'
    appPath: '<Path to the IOS app file>'
  displayName: 'Run BrowserStack Tests'

- task: MacStadiumVMs@0
  inputs:
    macStadiumServiceEndpoint: '<Your Mac Stadium Service Endpoint>'
    virtualMachineName: '<Your IOS Agent Name>'
    vmUsername: '<Your IOS Agent Username>'
    vmPassword: '<Your IOS Agent Password>'
  displayName: 'Mac Stadium IOS Agent Integration'

- task: CopyFiles@2
  inputs:
    SourceFolder: '<Path to the IOS app file>'
    Contents: '**'
    TargetFolder: '$(build.artifactstagingdirectory)'
  displayName: 'Copy files to artifact staging directory'

- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(build.artifactstagingdirectory)'
    ArtifactName: 'drop'
    publishLocation: 'Container'
  displayName: 'Publish Artifact'

- task: Xcode@5
  inputs:
    actions: 'archive'
    configuration: 'Release'
    sdk: 'iphoneos'
    scheme: '<Your IOS app scheme>'
    packageApp: true
    archivePath: '$(build.artifactstagingdirectory)/$(scheme).xcarchive'
  displayName: 'Xcode Archive'

- task: Xcode@5
  inputs:
    actions: 'export'
    configuration: 'Release'
    sdk: 'iphoneos'
    scheme: '<Your IOS app scheme>'
    exportPath: '$(build.artifactstagingdirectory)'
    exportOptions: 'plistFile'
    export

##################################################################################### 
  
- Install the required plugins: Install the SonarCloud plugin, BrowserStack plugin, and Mac Stadium IOS Agent Integration plugin in your Azure DevOps organization.
- This is a YAML file that contains a continuous integration pipeline for an iOS application. Through this pipeline, the application code is analyzed by SonarCloud and the results are published in a "quality gate".
- Then, automated tests are run on the BrowserStack platform and on an iOS agent from the MacStadium platform. The result of these tests is used to validate the quality of the code and the application.
- Finally, the app is packaged and exported in app store format for distribution.
- This pipeline can run in a continuous integration environment such as Azure DevOps or GitHub Actions.
  
  
   ### Showing a bit of how the app was in the test phase with some pages available within the app itself.
  
  ![image](https://user-images.githubusercontent.com/80284143/228021034-846819be-b593-4de7-a5e5-1985db43f44b.png)

  
  ![image](https://user-images.githubusercontent.com/80284143/228035498-c710397f-1ceb-45a3-b509-7c8dedb75f4a.png)

  
  ![image](https://user-images.githubusercontent.com/80284143/228035577-eb87204c-c411-4a79-9b16-1a381bb5ad93.png)

  
  ![image](https://user-images.githubusercontent.com/80284143/228035722-049ffd82-e112-4764-84ad-db3d8d485b76.png)

  
  ![image](https://user-images.githubusercontent.com/80284143/228035896-c5db2cb1-b165-4943-8753-1e9cd1f8dffd.png)
  
  
  #####################################################################################
  
  
  
  

  
  
  
