# GitHub Action for maven release

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
  TECH_USER_PASSWORD:
    description: 'Password of technical user'
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
  TECH_USER_USERNAME: 'speter555-robot'
  TECH_USER_EMAIL: 'szabo.peter.89.me@gmail.com'
  JAVA_VERSION: '17'
  JAVA_DISTRIBUTION: 'temurin'

jobs:
  mvn-release-main:
    runs-on: ubuntu-latest
    steps:
      - name: Release
        uses: speter555/gh-action-mvn-release-main@v0.16.0
        with:
          GITHUB_TOKEN: '${{ secrets.GITHUB_TOKEN }}'
          ARTIFACT_STORE_SERVER_ID: 'github'
          SETTINGS_XML_PATH: '.github/.m2/settings.xml'
          NEXUS_USER: '${{ secrets.NEXUS_USER }}'
          NEXUS_PASSWORD: '${{ secrets.NEXUS_PASSWORD }}' # Use github_token value if github artifact stores use 
          GPG_KEY_ID_GITHUB_TECHUSER: '${{ secrets.GPG_KEY_ID_GITHUB_TECHUSER }}'
          GPG_SIGNING_KEY_GITHUB_TECHUSER: '${{ secrets.GPG_SIGNING_KEY_GITHUB_TECHUSER }}'
          SSH_PRIVATE_KEY_GITHUB_TECHUSER: '${{ secrets.SSH_PRIVATE_KEY_GITHUB_TECHUSER }}'
          TECH_USER_USERNAME: '${{ secrets.TECH_USER_USERNAME }}'
          TECH_USER_EMAIL: '${{ secrets.TECH_USER_EMAIL }}'
          JAVA_VERSION: '17'
          JAVA_DISTRIBUTION: 'temurin'
```

## Development

The workflow action is composite workflow, that mean the workflow is in the root action.yml.

### Testing

For test the workflow changes, you can use another project, where you can use this repository in workflow step. Like there: https://github.com/speter555/test

### Release

Running the release will create its own release workflow, which handle the following automatic steps:
- started from main/master branch (e.g. `1.1.0-SNAPSHOT`): 
  - print variables
  - import GPG key and configure GIT for signed commit
  - create and push new release branch (with version `1.1.0-SNAPSHOT`)
  - perform maven release, tagging (`1.1.0`)
  - bump version for release branch (`1.1.1-SNAPSHOT`)
  - bump version for main/master and create new pull request (`1.2.0-SNAPSHOT`)
- started from release branch (e.g. `1.2.1-SNAPSHOT`):
  - print variables
  - import GPG key and configure GIT for signed commit
  - perform maven release, tagging (`1.2.1`)
  - bump version for release branch (`1.2.2-SNAPSHOT`)

Limitation: if you want to release MAJOR version (e.g. `1.0.0 -> 2.0.0`) first you have to update the version number manually in pom.xml before running the release workflow
 
