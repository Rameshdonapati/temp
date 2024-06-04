trigger:
  branches:
    include:
      - main
      - develop

pool:
  vmImage: 'windows-latest'

variables:
  JAVA_HOME: 'C:\\Program Files\\AdoptOpenJDK\\jdk-11.0.11.9-hotspot'
  MAVEN_OPTS: '-Xmx1024m'

stages:
  - stage: Build
    displayName: "Build Stage"
    jobs:
      - job: Build_Job
        displayName: "Build Job"
        steps:
          - task: UseJavaVersion@1
            inputs:
              versionSpec: '11'
              jdkArchitectureOption: 'x64'
          - task: Maven@3
            inputs:
              mavenPomFile: 'pom.xml'
              goals: 'clean compile'
              options: '-DskipTests'
              publishJUnitResults: false
              javaHomeOption: 'Path'
              mavenVersionOption: 'Default'
              mavenAuthenticateFeed: false

  - stage: Regression_Tests
    displayName: "Regression Tests Stage"
    dependsOn: Build
    jobs:
      - job: Regression_Tests_Job
        displayName: "Regression Tests Job"
        steps:
          - task: UseJavaVersion@1
            inputs:
              versionSpec: '11'
              jdkArchitectureOption: 'x64'
          - task: Maven@3
            inputs:
              mavenPomFile: 'pom.xml'
              goals: 'test'
              options: '-Dtest=**/Regression*Test'
              publishJUnitResults: true
              testResultsFiles: '**/surefire-reports/TEST-*.xml'
              javaHomeOption: 'Path'
              mavenVersionOption: 'Default'
              mavenAuthenticateFeed: false
          - task: PublishTestResults@2
            displayName: "Publish Regression Test Results"
            inputs:
              testResultsFormat: 'JUnit'
              testResultsFiles: '**/surefire-reports/TEST-*.xml'
              failTaskOnFailedTests: true

  - stage: Publish
    displayName: "Publish Stage"
    dependsOn: Regression_Tests
    condition: succeededOrFailed()
    jobs:
      - job: Publish_Job
        displayName: "Publish Job"
        steps:
          - powershell: |
              Write-Host "Publish artifacts or deploy"
            displayName: "Publishing Step"
