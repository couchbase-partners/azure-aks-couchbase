## Couchbase Cluster on Azure Container Instances (ACI) 
___
#### What are Azure Container Instances
ACI containers are conceptually similar to pods in Kubernetes.  While not truly orchestration, ACI
allows grouping around first-class container objects. The containers within the group can communicate with each other on a private network by default. 
External communication is established via a randomly assigned public IP.<br>
It is important to be aware that ACI is in preview and is not for production use.  
#### Why use ACI
ACI allows you to easily run containers on Azure with a single command. There is no managing of Virtual 
Machines and other infrastructure components.  [Billing](https://azure.microsoft.com/en-us/pricing/details/container-instances/) 
is at a granularity of the duraction of cores, memory and requests.  This is more flexible and accurate than the standard
set VM size approach.

#### Prerequisites:

* A Microsoft Azure account. Create an [account](https://azure.microsoft.com/en-us/free/) if required.

The [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/limitations) is command-line for this example so that the Azure Portal can be used exclusively.

![Azure Cloud Shell Screenshot](https://github.com/sliburd/azure-container-instances-couchbase/blob/master/screenshots/Azure_Cloud_Shell.PNG)

#### Create a resource group
```
s@Azure:~$ az group create -l eastus -n couchbaseonaciRG
```
After successful creating the resource group the console prints the output:
```
{
  "id": "/subscriptions/XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXXX/resourceGroups/couchbaseonaciRG",
  "location": "eastus",
  "managedBy": null,
  "name": "couchbaseonaciRG",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

#### Create an ACI using the cloud shell:
![create aci image](https://github.com/sliburd/azure-container-instances-couchbase/blob/master/screenshots/created_ACI_using_command_line_creating_status.PNG)

The provisioningState specifies "*Creating*".<br>
Wait about a minute then execute the container show command to make sure the instance has been provisioned successfully:

```
s@Azure:~$ az container show --name acicbexample -g couchbaseonaciRG
```
Pay attention to the port, IP and provisioningState (should be *"Succeeded"*).
![after creation of aci](https://github.com/sliburd/azure-container-instances-couchbase/blob/master/screenshots/aci_created__port_ip_status.PNG)

We can begin starting the cluster by opening **http://\<IP\>:8091** in a web browser.

![setup](https://github.com/sliburd/azure-container-instances-couchbase/blob/master/screenshots/Couchbase_Setup_page.PNG)

#### Start the cluster via the Web Console:
1. Select "*Start a new cluster*".
2. Set the "*Data RAM Quota*" and "*Index RAM Quota*" to 512 MB.
3. Leave the other fields as the defaults and hit "*Next*".
4. Choose the choose the "beer-sample" bucket and continue to hit "*Next*".
5. Continue to hit "*Next*" following the instructions.
6. On the final step enter a username and password for the administrator account.
![selecting beer-sample](https://github.com/sliburd/azure-container-instances-couchbase/blob/master/screenshots/pick_beer_sample_bucket.PNG)
![final configure](https://github.com/sliburd/azure-container-instances-couchbase/blob/master/screenshots/Final_configure_step.PNG)

The cluster is running, you will notice that you have one active server. 
#### Run a query
We will use data from the beer-sample bucket chosen during setup for a N1QL query:
1. Navigate to the "*Query*" tab.
2. Type "select `beer-sample`.* from `beer-sample`;" (without the quotes) in the query text box and hit "*Execute*". 
3. Observe the results and explore the different output format tabs.
![query results]()

  #### Clean up
  Tear-down is simply deleting the container using the command:
  
  ```s@Azure:~$ az container delete --name acicbexample --resource-group couchbaseonaciRG --yes```
  
  Now we can do a check that the container is gone by the command:
  ![container show after delete](https://github.com/sliburd/azure-container-instances-couchbase/blob/master/screenshots/container_show_after_delete.PNG)
  The command returned nothing, which indicates the container no longer exists.
  
  That is it!  We are finished.  Using ACI is that simple and easy.
  
  
