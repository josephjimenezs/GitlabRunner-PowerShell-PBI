# GitlabRunner-PowerShell-PowerBI

![Project Logo](link-to-your-logo.png)

## Overview

This project describes all elements and steps related to Image creation and usage for the josephjimenezs/gitlabrunner-powershell-powerbi:1.0 image located on dockerhub.
If you want just configure and use, please go to step 3:  *Usage*.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Creation](#creation)
3. [Usage](#usage)
4. [Configuration](#configuration)
5. [Contributing](#contributing)
6. [License](#license)
7. [Contact](#contact)

## Prerequisites

Before you begin, ensure you have met the following requirements:

- [ ] You have installed Docker
- [ ] You have a Power BI account
- [ ] You have access to a GitLab repository
- [ ] You have access to a DockerHub repository

## Installation

Follow these steps to set up your project:

### Step 1: Clone the Repository
        https://github.com/josephjimenezs/GitlabRunner-PowerShell-PBI.git

### Step 2: Create the Image from the docker file and push to dockerhub

#### Step 2.1 Image Creation
        docker build --pull --rm -f "docker" -t yournewimage:1.0 "." 
#### Step 2.2 Image Publication to DockerHub
        docker login
        docker tag yournewimage:1.0 dockerhubrepository/yournewimage:1.0
        docker push dockerhubrepository/yournewimage:1.0

## Usage

### Step 1: Install LocalGitlabRunner (windows environment)
        New-Item -Path 'c:/Docker/GitlabRunner' -ItemType Directory
        cd 'C:\GitLab-Runner'
        Invoke-WebRequest -Uri "https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-windows-amd64.exe" -OutFile "gitlab-runner.exe"
        
### Step 2: Create a container, using the image created and the runner folder
       docker run -d --name gitlab-runner-pbi -v /var/run/docker.sock:/var/run/docker.sock -v c:/Docker/GitlabRunner:/etc/gitlab-runner -it josephjimenezs/gitlabrunner-powershell-powerbi:1.0

### Step 3: Gitlab runner container configuration
First must go to Gitlab and create a new runner, get your server url and registration token to connect your runner with gitlab's runner.

        docker exec -it gitlab-runner-pbi gitlab-runner register --non-interactive --executor "docker" --shell "pwsh" --docker-image "josephjimenezs/gitlabrunner-powershell-powerbi:1.0" --url "yourgitabserver" --description "pwsh-docker-runner" --tag-list "pwsh,docker" --locked="false" --access-level="not_protected" --registration-token="yourtoken"


### Step 4: Container Tools Verification
        docker exec -it gitlab-runner-pbi pwsh
        $PSVersionTable
        gitlab-runner --version
        Get-InstalledModule


### Step 5: Create the PowerShell File
        # Define variables
        $PbiUser = "userName"
        $PbiPassword = "Password"
        $WorkspaceName = "WorkspaceName"
        $PbixFilePath = "ReportRoute"
        $ReportName = "ReportName"

        # Import Power BI modules
        Import-Module MicrosoftPowerBIMgmt
        Import-Module MicrosoftPowerBIMgmt.Profile

        # Convert password to a secure string
        $PbiSecurePassword = ConvertTo-SecureString $PbiPassword -AsPlainText -Force

        # Create a credential object
        #$PbiCredential = New-Object -TypeName PSCredential -ArgumentList $PbiUser, $PbiSecurePassword
        $PbiCredential = New-Object PSCredential($PbiUser, $PbiSecurePassword)

        ##################################################################################
        # Log in to Power BI using the stored credentials
        Login-PowerBIServiceAccount -Credential $PbiCredential

        # Check if the connection was successful
        if ($? -eq $false) {
               Write-Error "Failed to connect to Power BI Service Account using stored credentials."
               exit 1
           }
        #################################################################################

        # Connect to the Power BI service
        Connect-PowerBIServiceAccount -Credential $PbiCredential

        # Retrieve the workspace
        $WorkspaceObject = Get-PowerBIWorkspace -Scope Organization -Name $WorkspaceName


        try {
            # Deploy pbix file
            New-PowerBIReport -WorkspaceId $WorkspaceObject.Id -Path $PbixFilePath -Name $ReportName -ConflictAction CreateOrOverwrite | Out-Null
        } catch {
            # Output an error message
            Write-Output "Error found"
        }


### Step 6: Reference the powershell file on the yaml file
#### .gitlab-ci.yml:

        image:
          name: josephjimenezs/gitlabrunner-powershell-powerbi:1.0
          entrypoint: [""]		
        stages:
          - build

        variables:
          SMTP_USERNAME: ${SMTP_USERNAME}
          SMTP_PASSWORD: ${SMTP_PASSWORD} 

        build:
          stage: build
          script:
            - echo "Starting the process"
            - pwsh -ExecutionPolicy Bypass -File CD.ps1  	
            - echo "Process Completed"


## License
Please refer to license file on this repository.
