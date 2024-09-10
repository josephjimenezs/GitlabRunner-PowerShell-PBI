# Project Name

![Project Logo](link-to-your-logo.png)

## Overview

Briefly describe what your project does, its purpose, and its main features.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
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

### Step 2: Create the Image from the docker file:
        docker build --pull --rm -f "docker" -t gitlabrunnerpowershellpbi:latest "." 

### Step 3: Create a shared folder
        
## Usage

### Step 1: Reference the powershell file on the yaml file
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
