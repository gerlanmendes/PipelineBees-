# PipelineBees

### Azure devops pipeline for app testing

This pipeline aims to run Sonar Cloud and in case of validation below the quality rule, the application will stop.
Use BrowserStack during compilation using the artifact in local environment.
Use the MAC Stadium IOS agent as an integration.


------------------------------------------------------------------------------------------------------------------------------------------------


## Here below is the pipeline to use Azure Devops and GithubAction.


  trigger:

  main

  pool:
    vmImage: 'macOS-latest'

  steps:
    task: SonarCloudPrepare@1
    inputs:
      SonarCloud: 'SonarCloud Connection'
      organization: '<Your SonarCloud Organization>'
      scannerMode: 'CLI'
      configMode: 'manual'
      cliProjectKey: '<Your SonarCloud Project Key>'
      cliProjectName: '<Your SonarCloud Project Name>'
      cliSources: '.'
    displayName: 'Prepare analysis on SonarCloud'

    task: SonarCloudAnalyze@1
    displayName: 'Code Analysis'

    task: SonarCloudPublish@1
    inputs:
      pollingTimeoutSec: '300'
    displayName: 'Publish Quality Gate Result'

    task: BrowserStack@1
    inputs:
      credentialsType: 'ServiceEndpoint'
      browserStackServiceEndpoint: '<Your BrowserStack Service Endpoint>'
      browserStackUserName: '<Your BrowserStack Username>'
      browserStackAccessKey: '<Your BrowserStack Access Key>'
      appPath: '<Path to the IOS app file>'
    displayName: 'Run BrowserStack Tests'

    task: MacStadiumVMs@0
    inputs:
      macStadiumServiceEndpoint: '<Your Mac Stadium Service Endpoint>'
      virtualMachineName: '<Your IOS Agent Name>'
      vmUsername: '<Your IOS Agent Username>'
      vmPassword: '<Your IOS Agent Password>'
    displayName: 'Mac Stadium IOS Agent Integration'

    task: CopyFiles@2
    inputs:
      SourceFolder: '<Path to the IOS app file>'
      Contents: '**'
      TargetFolder: '$(build.artifactstagingdirectory)'
    displayName: 'Copy files to artifact staging directory'

    task: PublishBuildArtifacts@1
    inputs:
      PathtoPublish: '$(build.artifactstagingdirectory)'
      ArtifactName: 'drop'
      publishLocation: 'Container'
    displayName: 'Publish Artifact'

    task: Xcode@5
    inputs:
      actions: 'archive'
      configuration: 'Release'
      sdk: 'iphoneos'
      scheme: '<Your IOS app scheme>'
      packageApp: true
      archivePath: '$(build.artifactstagingdirectory)/$(scheme).xcarchive'
    displayName: 'Xcode Archive'

    task: Xcode@5
    inputs:
      actions: 'export'
      configuration: 'Release'
      sdk: 'iphoneos'
      scheme: '<Your IOS app scheme>'
      exportPath: '$(build.artifactstagingdirectory)'
      exportOptions: 'plistFile'
      export


------------------------------------------------------------------------------------------------------------------------------------------------ 


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
  
  
  ------------------------------------------------------------------------------------------------------------------------------------------------
  
  
  
  ### To manually configure an Apple application for testing, you can follow these steps:

- Obtain a developer account: You will need a valid Apple Developer account to create and test your application. You can sign up for an account on the Apple Developer website.
- Set up a development environment: You will need a development environment that includes Xcode, Apple's integrated development environment (IDE). You can download Xcode from the Mac App Store or the Apple Developer website.

- Create a new project: Open Xcode and create a new project. Choose a template that matches the type of application you want to create.

- Configure your project settings: Configure your project settings, such as the project name, team, and bundle identifier. You can access these settings by selecting your project in the Project navigator and selecting the project name.

- Configure your build settings: Configure your build settings, such as the target device, code signing, and provisioning profiles. You can access these settings by selecting your target in the Project navigator and selecting the Build Settings tab.

- Add code to your application: Write the code for your application using the programming language and frameworks of your choice.

- Run your application: Connect a test device to your development computer and run your application on the device. You can do this by selecting your device in the Xcode toolbar and clicking the Run button.

- Test your application: Test your application on your device and make any necessary changes to your code.

- Publish your application: When you are satisfied with your application, you can publish it to the App Store or distribute it to your users using ad-hoc distribution or enterprise distribution.

- Note that these steps provide a general overview of the process, and the specific steps may vary depending on the type of application you are creating and the requirements of your project.



------------------------------------------------------------------------------------------------------------------------------------------------
  
  
  ### Declaring pipelines in stages
  
    
    
    
  
- The first task, SonarCloudAnalyze@1, involves code analysis using SonarCloud tools to detect issues such as bugs, security vulnerabilities and code smells. This task produces a report of problems found in the code.

        
        trigger:

          main

          pool:
            vmImage: 'macOS-latest'

          steps:
            task: SonarCloudPrepare@1
            inputs:
              SonarCloud: 'SonarCloud Connection'
              organization: '<Your SonarCloud Organization>'
              scannerMode: 'CLI'
              configMode: 'manual'
              cliProjectKey: '<Your SonarCloud Project Key>'
              cliProjectName: '<Your SonarCloud Project Name>'
              cliSources: '.'
            displayName: 'Prepare analysis on SonarCloud'
    
    
    
    ------------------------------------------------------------------------------------------------------------------------------------------------
    
    
    
- The second task, SonarCloudPublish@1, publishes the results of the analysis to the SonarCloud platform, which can then be used to track the quality of the codebase over time. The pollingTimeoutSec input specifies how long the task should wait for the analysis to complete before timing out. The displayName simply provides a descriptive name for the task in the pipeline.   
  
  
  
             task: SonarCloudAnalyze@1
              displayName: 'Code Analysis'

              task: SonarCloudPublish@1
              inputs:
                pollingTimeoutSec: '300'
              displayName: 'Publish Quality Gate Result'

    
    ------------------------------------------------------------------------------------------------------------------------------------------------
    
    - Together, these two tasks help to ensure that the code being developed is of high quality and free from issues that could cause problems down the line.
    
    ------------------------------------------------------------------------------------------------------------------------------------------------
    
- This task is related to running tests on the BrowserStack platform.

The BrowserStack@1 task sets up a connection to the BrowserStack service using the specified browserStackServiceEndpoint credentials, which would include a browserStackUserName and browserStackAccessKey for authentication. The appPath input specifies the location of the iOS app file that will be tested on the BrowserStack platform. 
Overall, this task enables developers to easily run tests on a remote platform and receive results quickly, without having to set up and maintain their own testing infrastructure.
    
    
            task: BrowserStack@1
            inputs:
              credentialsType: 'ServiceEndpoint'
              browserStackServiceEndpoint: '<Your BrowserStack Service Endpoint>'
              browserStackUserName: '<Your BrowserStack Username>'
              browserStackAccessKey: '<Your BrowserStack Access Key>'
              appPath: '<Path to the IOS app file>'
            displayName: 'Run BrowserStack Tests'

    
    
    ------------------------------------------------------------------------------------------------------------------------------------------------
    
- This task is related to configuring a virtual machine on a MacStadium service endpoint to be used as an agent for iOS development and testing.
The MacStadiumVMs@0 task configures a virtual machine on the specified MacStadium service endpoint with the inputs specified, such as the virtualMachineName, vmUsername, and vmPassword. These inputs are used to authenticate and connect to the virtual machine.
The displayName input provides a descriptive name for the task in the pipeline.
Overall, this task is useful for setting up a virtual machine on a MacStadium service endpoint that can be used as an agent for iOS development and testing, allowing developers to test their apps on real iOS devices and ensure that they work properly before release.

      
          task: MacStadiumVMs@0
        inputs:
          macStadiumServiceEndpoint: '<Your Mac Stadium Service Endpoint>'
          virtualMachineName: '<Your IOS Agent Name>'
          vmUsername: '<Your IOS Agent Username>'
          vmPassword: '<Your IOS Agent Password>'
        displayName: 'Mac Stadium IOS Agent Integration'
      
      
    ------------------------------------------------------------------------------------------------------------------------------------------------
    
-  The CopyFiles@2 task takes the files located in the SourceFolder specified and copies all files and subdirectories that match the Contents pattern to the TargetFolder. The $(build.artifactstagingdirectory) is a predefined variable that represents the artifact staging directory in the pipeline, where the output files and artifacts of the build process can be stored.

The displayName input provides a descriptive name for the task in the pipeline.

Overall, this task is useful for moving files from one location to another within a pipeline, for example, copying the build output to an artifact staging directory for further use or storage.


           task: CopyFiles@2
          inputs:
            SourceFolder: '<Path to the IOS app file>'
            Contents: '**'
            TargetFolder: '$(build.artifactstagingdirectory)'
          displayName: 'Copy files to artifact staging directory'


  ------------------------------------------------------------------------------------------------------------------------------------------------  


- The PublishBuildArtifacts@1 task likely takes the files located in the PathtoPublish folder and publishes them as an artifact with the name specified by the ArtifactName input. The $(build.artifactstagingdirectory) is a predefined variable that represents the artifact staging directory in the pipeline, where the output files and artifacts of the build process can be stored.

The publishLocation input specifies where the artifact will be published, in this case, to a container. The displayName input provides a descriptive name for the task in the pipeline.

Overall, this task is useful for publishing the output of a build process as an artifact, which can then be used in subsequent pipeline tasks or downloaded for further use or storage.


            task: PublishBuildArtifacts@1
          inputs:
            PathtoPublish: '$(build.artifactstagingdirectory)'
            ArtifactName: 'drop'
            publishLocation: 'Container'
          displayName: 'Publish Artifact'

    
  ------------------------------------------------------------------------------------------------------------------------------------------------
  
  
- The Xcode@5 task likely invokes the Xcode command-line tools with the specified inputs to perform an archive action on the iOS app project specified by the scheme. The configuration input specifies which build configuration to use, Release in this case. The sdk input specifies which iOS SDK to use, iphoneos in this case.
The packageApp input indicates that the app should be packaged after it is built. The archivePath input specifies the location where the resulting .xcarchive file should be saved, which in this case is the artifact staging directory.
The displayName input provides a descriptive name for the task in the pipeline.
Overall, this task is useful for building and archiving an iOS app in a pipeline, which can then be published or distributed to users.  

      
          task: Xcode@5
        inputs:
          actions: 'archive'
          configuration: 'Release'
          sdk: 'iphoneos'
          scheme: '<Your IOS app scheme>'
          packageApp: true
          archivePath: '$(build.artifactstagingdirectory)/$(scheme).xcarchive'
        displayName: 'Xcode Archive'



------------------------------------------------------------------------------------------------------------------------------------------------


- The configuration input specifies which build configuration to use, Release in this case. The sdk input specifies which iOS SDK to use, iphoneos in this case.
The exportPath input specifies the location where the resulting package should be saved, which in this case is the artifact staging directory. The exportOptions input specifies the method to use for exporting the app, which is specified in the plistFile input.
The export input indicates that the app should be exported after it is built.
Overall, this task is useful for exporting an iOS app archive as a distribution-ready package that can be submitted to the App Store or distributed through other channels.

          task: Xcode@5
        inputs:
          actions: 'export'
          configuration: 'Release'
          sdk: 'iphoneos'
          scheme: '<Your IOS app scheme>'
          exportPath: '$(build.artifactstagingdirectory)'
          exportOptions: 'plistFile'
        export
