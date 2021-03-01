# Github Action for CI/CD

> A simple deployment script for implementing CI/CD in your projects

## 1. Prepare SSH Key

```bash
# In your Local Machine
ssh-keygen -t rsa

# copy the private key
cat ~/.ssh/id_rsa

# Go to your github projects > Settings > Secrets
# Create a Secret with the private key
# Example: SSH_KEY

# Copy the public key
cat ~/.ssh/id_rsa.pub

# SSH to your remote machine
ssh root@0.0.0.0

# edit the authorized keys file
# paste the public key at the end
vim ~/.ssh/authorized_keys
```

## 2. Create Github action in YML file

```bash
# In your local project
# Create a folder called .github
# Create a folder called workflows under .github/
# Create a YML file with any name under .github/workflows/deploy.yml
touch deploy.yml
```

## 3. Write the rules and jobs to be done automatically

```yml
name: Name_of_the_action

# For manual dispatch an action
# append workflow_dispatch

# For automation
# append the branch to be listened by the action
on:
    # Automation
    push:
        branches: [master]
    # Manual
    workflow_dispatch:
        inputs:
            # Variables
            variableA:
                description: "Input Label"
                required: true

# Declare the jobs to be performed
jobs:
    # Job Name
    pull_repo:
        # Descriptive Name
        name: Pull Repository
        runs-on: ubuntu-latest
        steps:
            # Steps


# To access the workflow variable
# ${{github.event.inputs.VARIABLE_NAME}}

# To access the SECRETS
# ${{Secrets.SECRET_NAME}}

# String Comparison in If clause
# if: contains(VAR, VAR)

# Key steps:
uses: actions/checkout@v2
uses: actions/setup-node@v1
    with:
        node-version: "14.x"
uses: appleboy/ssh-action@master
    with:
        host: ip address
        username: root
        key: ${{ secrets.SSH_KEY }}
        port: 22
        script: |
            your commands
```
