# Azure Kubernetes Service (AKS)

These instructions detail how to deploy a on Azure with Kubernetes.  With that complete, the next step is to setup the operator and then create a Couchbase cluster using the operator.

We're making use of [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/blog/introducing-azure-container-service-aks-managed-kubernetes-and-azure-container-registry-geo-replication/).  AKS is currently in preview.  Because of that, AKS must be explicitly enabled for the subscription you are working on.  Detailed instructions on how to do that are [here](https://blogs.msdn.microsoft.com/alimaz/2017/10/24/enabling-aks-in-your-azure-subscription/).

In short, you need to run the Azure Power Shell command:

    Register-AzurermresourceProvider --ProviderNamespace Microsoft.ContainerService

With that complete you can run an AKS command with the Azure CLI 2.0 to create a cluster:

    az aks create -n MyCluster -g MyResourceGroup

To setup kubectl run:

    az aks get-credentials -n MyCluster -g aks

# Operations Guide

## Install the Couchbase Operator

OPTIONAL: If you are using a private registry, make sure to deploy the registrykey secret first.  Create the 'registrykey' secret:

```bash
$ kubectl apply -f example/registrykeysecret.yaml
```

Create deployment: In order to run a couchbase cluster you'll first need to deploy a custom resource definition
which will allow for the creation of a 'couchbasecluster' type within the kubernetes cluster.

```bash
$ kubectl create -f example/deployment.yaml
```

Create the 'cb-example-auth' secret

```bash
$ kubectl apply -f example/secret.yaml
```

Now objects with "kind: CouchbaseCluster" can be created.  Create a cluster:

```bash
$ kubectl apply -f example/couchbase-cluster.yaml
```

couchbase operator will automatically create a Kubernetes Custom Resource Definition (CRD):

```bash
$ kubectl get couchbasecluster
NAME         KIND
cb-example   CouchbaseCluster.v1beta1.couchbase.database.couchbase.com
```

With these steps complete, you can follow the steps in the [Administration Guide](administrationGuide.md) to connect to the cluster.

# Administration Guide

## Accessing the Administration Console

To access the administration console you can run the following command on any pod, for example:

    kubectl port-forward cb-example-0000 8091:8091

This command will make the Administration available on 127.0.0.1:8091. Note that you will not be able to access other nodes in your cluster since this command will only proxy you through to the specified node.

The username and password for Couchbase on port 8091 are set in your secret.yaml file.  By default they are Administrator and password.

## Getting Server Logs

The easiest way to get cbcollect logs is to access the administration console and click the "Collect Logs" button in the Logs tab. This will give you the path to the corresponding zip file for each node in the cluster. You can then run a command like the one below against each node in the cluster to collect their logs.

    kubectl cp <namespace>/<pod_name>:<path_to_logs> -c couchbase-server ./logs.zip

An example of what this might look like is below.

    kubectl cp default/cb-example-0000:/opt/couchbase/var/lib/couchbase/tmp/collectinfo-2017-09-28T175135-ns_1@127.0.0.1.zip -c couchbase-server ./logs.zip
