# azure-kubernetes-couchbase

This is a walkthrough of setting the [Couchbase Operator](https://blog.couchbase.com/introducing-couchbase-operator/) up on [Azure Container Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/).

## Deploy an AKS Cluster

AKS is currently in public preview.  There are a bunch of [nice tutorials](https://docs.microsoft.com/en-us/azure/aks/) on how to use it.

For this walkthrough we're going to use the Azure 2.0 CLI.  If you haven't already, you'll need to [install that and login](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli).  Even if you have the CLI installed already, you might want to update it.  To make sure the Azure 2.0 CLI is working properly try running:

    az group list

With that all set, we can register the AKS provider, create a resource group and an AKS cluster:

    az provider register -n Microsoft.ContainerService
    az group create --name myResourceGroup --location eastus
    az aks create --resource-group myResourceGroup --name myAKSCluster

Note that it's way easier to do this from the CLI because the CLI creates the service principal you need automatically.  If you do this in the portal you'll need to create the service principal manually.

![aks-deploying](/images/aks-deploying.png)

When that all finishes you should see something like this:

XXX

You can now login to the [Azure Portal](https://portal.azure.com) and take a look at your new cluster there as well:

XXX

## Deploying the Operator

Once you have an AKS cluster deployed and a running kubectl, you're ready to deploy the Operator.  The documentation on that is [here](http://docs.couchbase.com/prerelease/couchbase-operator/beta/overview.html).

To create the deployment, run this:

    kubectl create -f https://s3.amazonaws.com/packages.couchbase.com/kubernetes/beta/operator.yaml

Now check that it worked:

    kubectl get deployments
