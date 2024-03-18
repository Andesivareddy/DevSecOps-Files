1) Create Custom Quality Gate in SonarCloud and Add conditions to the Quality Gate
2) Assign this Quality Gate to the Project
3) Add script in azure-pipeline.yaml file to enable quality gate check (Note: This will fail your build in case Quality Gate fails)

sleep 5
sudo apt update
sudo apt install curl jq 
quality_status=$(curl -s -u 14ad4797c02810a818f21384add02744d3f9e34d: https://sonarcloud.io/api/qualitygates/project_status?projectKey=azuredevopsadodevsecopsprojectkey | jq -r '.projectStatus.status')
echo "SonarCloud Analysis Status is $quality_status"; 
if [[ $quality_status == "ERROR" ]] ; then exit 1;fi


Script for Code Quality Gates

trigger:
- master

pool:
  vmImage: ubuntu-latest

jobs:
- job: run_sonarcloud_with_java_17
  displayName: 'Run SonarCloud Analysis with Java Version 17'
  steps:
  - task: JavaToolInstaller@0
    inputs:
      versionSpec: '17'
      jdkArchitectureOption: 'x64'
      jdkSourceOption: 'PreInstalled'

  - script: |
      sudo apt-get update
      sudo apt-get -y install curl jq
      mvn verify package sonar:sonar -Dsonar.host.url=https://sonarcloud.io/ -Dsonar.organization=azuredevopsdevsecopsoorg -Dsonar.projectKey=azuredevsecopsoprojectkey -Dsonar.token=d268968115b4935bcd007ff54f01906c18e33cb6
      sleep 5
      quality_status=$(curl -s -u d268968115b4935bcd007ff54f01906c18e33cb6: https://sonarcloud.io/api/qualitygates/project_status?projectKey=azuredevsecopsoprojectkey | jq -r '.projectStatus.status')
      echo "SonarCloud analysis status is $quality_status"; 
      if [[ $quality_status == "ERROR" ]] ; then exit 1;fi
    displayName: "Integrate SAST using SonarCloud to populate code coverage in Azure DevOps DevSecOps Pipeline"