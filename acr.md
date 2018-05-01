# Azure Container Registry (ACR)

This document describes how to publish a container image in the Azure Container Marketplace.  A typical user should not need to do this.

Documentation on ACR is available [here](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-azure-cli).

You'll need to have Docker installed to follow these instructions.

Create a resource group and an ACR

    az group create --name marketplaceacr --location eastus
    az acr create --resource-group marketplaceacr --name couchbase --sku Basic

Log in to a container registry through the Docker CLI

    az acr login --name couchbase

Download image from Docker Hub

    docker pull couchbase

Get the login server name and then push the container to ACR with that name

    az acr list --resource-group marketplaceacr --query "[].{acrLoginServer:loginServer}" --output table
    docker tag couchbase couchbase.azurecr.io/couchbase
    docker push couchbase.azurecr.io/couchbase

Now we can check the image is present with

    az acr repository list --name couchbase --output table

Get the credentials

    az acr update --name couchbase --admin-enabled true
    az acr credential show --name couchbase

Deploy a container using ACR

    az container create --resource-group marketplaceacr --name cb1 --image marketplaceacr.azurecr.io/couchbase --cpu 1 --memory 1 --registry-username <name> --registry-password <password> --dns-name-label testdeploy --ports 8091
    az container show --resource-group marketplaceacr --name cb1 --query instanceView.state #--> "Pending"/"Running"
    az container show --resource-group marketplaceacr --name cb1 --query ipAddress.fqdn #--> "testdeploy.eastus.azurecontainer.io"

Couchbase Server should be running on:
http://testdeploy.eastus.azurecontainer.io:8091/ui/index.html
