# GitHub Action for maven release from main branch

## Input

```yaml
inputs:
  GITHUB_TOKEN:
    description: 'GITHUB_TOKEN or a `repo` scoped Personal Access Token (PAT)'
    default: ${{ github.token }}
  NEXUS_USER:
    description: 'Nexus user for upload artifacts to nexus'
    required: true
  NEXUS_PASSWORD:
    description: 'Password of nexus user for upload artifacts to nexus'
    required: true
  ARTIFACT_STORE_SERVER_ID:
    description: 'Value of the distributionManagement/repository/id field of the pom.xml'
    required: true
  SETTINGS_XML_PATH:
    description: 'settings.xml path in repository, for example: .github/.m2/settings.xml (in repository root, there is a .github folder ...'
    required: false
    default: '.github/.m2/settings.xml'
  GPG_KEY_ID_GITHUB_TECHUSER:
    description: 'Gpg key id of technical user'
    required: true
  GPG_SIGNING_KEY_GITHUB_TECHUSER:
    description: 'Gpg private key of technical user with base64 decode format'
    required: true
  SSH_PRIVATE_KEY_GITHUB_TECHUSER:
    description: 'Ssh private key of technical user in PEM foramat'
    required: true
  TECH_USER_USERNAME:
    description: 'Username of technical user'
    required: true
  TECH_USER_EMAIL:
    description: 'Email address of technical user'
    required: true
  JAVA_VERSION:
    description: 'Java version (default is 17)'
    required: false
    default: '17'
  JAVA_DISTRIBUTION:
    description: 'Java distribution (default is temurin)'
    required: false
    default: 'temurin'
```

## Usage

```yaml
name: Manual Run automatic Maven Release

on:
  workflow_dispatch:

env:
  TECH_USER_USERNAME: 'innovitech-robot'
  TECH_USER_EMAIL: 'github-techuser@innovitech.hu'
  JAVA_VERSION: '17'
  JAVA_DISTRIBUTION: 'temurin'

jobs:
  mvn-release-main:
    runs-on: ubuntu-latest
    steps:
      - name: Release
        uses: speter555/gh-action-mvn-release-main@gh-action-mvn-release-main-0.5.0
        with:
          NEXUS_USER: '${{ env.TECH_USER_USERNAME }}'
          NEXUS_PASSWORD: '${{ secrets.GITHUB_TOKEN }}'
          ARTIFACT_STORE_SERVER_ID: 'github'
          SETTINGS_XML_PATH: '.github/.m2/settings.xml'
          GPG_KEY_ID_GITHUB_TECHUSER: '${{ secrets.GPG_KEY_ID_GITHUB_TECHUSER }}'
          GPG_SIGNING_KEY_GITHUB_TECHUSER: '${{ secrets.GPG_SIGNING_KEY_GITHUB_TECHUSER }}'
          SSH_PRIVATE_KEY_GITHUB_TECHUSER: '${{ secrets.SSH_PRIVATE_KEY_GITHUB_TECHUSER }}'
          TECH_USER_USERNAME: '${{ env.TECH_USER_USERNAME }}'
          TECH_USER_EMAIL: '${{ env.TECH_USER_EMAIL }}'
          JAVA_VERSION: '17'
          JAVA_DISTRIBUTION: 'temurin'
```

## Development

The workflow action is composite workflow, that mean the workflow is in the root action.yml.

### Testing

For test the workflow changes, you can use another project, where you can use this repository in workflow step. Like there: 

### Release

Release is create an own release workflow, which handle tagging, branching etc.
Now release workflow can only main release! 
