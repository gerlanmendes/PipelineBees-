# PipelineBees

### Azure devops pipeline for app testing

Esse pipeline tem como objetivo executar o Sonar Cloud e em caso de validação abaixo da regua de qualidade, a aplicação irá parar. 
Utilizar o BrowserStack durante a comílação utilizando o artefato em ambiente local. 
Utilizar o agente do MAc Stadium IOS como integração. 

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
  
- Instale os plug-ins necessários: instale o plug-in SonarCloud, o plug-in BrowserStack e o plug-in de integração do agente Mac Stadium IOS em sua organização Azure DevOps.  
- Esse é um arquivo YAML que contém um pipeline de integração contínua para uma aplicação iOS. Através desse pipeline, o código da aplicação é analisado pelo SonarCloud e os resultados são publicados em um "quality gate".
- Em seguida, testes automatizados são executados na plataforma BrowserStack e em um agente iOS da plataforma MacStadium. O resultado desses testes é usado para validar a qualidade do código e do aplicativo.
- Por fim, o aplicativo é empacotado e exportado em formato de loja de aplicativos para ser distribuído. 
- Esse pipeline pode ser executado em um ambiente de integração contínua, como o Azure DevOps ou o GitHub Actions.
  
  
  ### Mostrando um pouco de como ficou o aplicativo em fase de teste com algunas páginas disponiveis dentro do proprio app. 
  
  
  ![image](https://user-images.githubusercontent.com/80284143/228021034-846819be-b593-4de7-a5e5-1985db43f44b.png)

  
  ![image](https://user-images.githubusercontent.com/80284143/228035498-c710397f-1ceb-45a3-b509-7c8dedb75f4a.png)

  
  ![image](https://user-images.githubusercontent.com/80284143/228035577-eb87204c-c411-4a79-9b16-1a381bb5ad93.png)

  
  ![image](https://user-images.githubusercontent.com/80284143/228035722-049ffd82-e112-4764-84ad-db3d8d485b76.png)

  
  ![image](https://user-images.githubusercontent.com/80284143/228035896-c5db2cb1-b165-4943-8753-1e9cd1f8dffd.png)

  
  
  
