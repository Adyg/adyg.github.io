---
layout: post
title: "Django - From Heroku To Azure: Deploying Infrastructure"
categories: development
tags: django,azure
---

First thing - get the Azure services setup and ready for serving the Django project. Manually creating everything via the Azure UI was not an option, since I needed something that can easily and consistently be reproduced across various development & testing environments.

I used [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/) to automate the resource deployment. It allows automating the deployment of any Azure services that comes with an API.

1. **Install the Azure CLI** (a good step by step [here](https://www.opsgility.com/blog/2016/01/20/install-azure-cli-tool-ubuntu/) or [here](https://web.archive.org/web/20170405205220/https://www.opsgility.com/blog/2016/01/20/install-azure-cli-tool-ubuntu/))

2. **Create a new resource group**:

   a. Command for creating a new resource group is:

    ```sh
    azure group create -n {group_name} -l {region_name}
    ```

   b. Ensure all the services you are going to deploy are available in the region you choose - consult the [Azure Products by Region list](https://azure.microsoft.com/en-us/regions/services/)

   c. The {region_name} will be a slug of the actual region name, e.g: East Us will be eastus. You can obtain a list of all locations and their slugs by running:

    ```sh
    azure location list
    ```
3. **Deploy the infrastructure**:

   ```sh
   azure group deployment create {group_name} {name} -f infrastructure_template.json
   ```

   The infrastructure_template.json is an [Azure Resource Manager template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates). The template I used for deploying everything described <a href="{% post_url 2017-04-03-django-on-azure %}">here</a> is available [on GitHub](https://github.com/Adyg/azure-arm-web-app/blob/master/infrastructure-template.json). Beyond deploying the Azure services, it also populates the Azure Web Application settings with Django specific settings.

4. **Create the Azure Storage container**. Unfortunatelly, this has to be done manually - ARM does not allow automating the creation of a storage container. There is a [request to have it implemented](https://feedback.azure.com/forums/281804-azure-resource-manager/suggestions/9306108-let-me-define-preconfigured-blob-containers-table), but until a solution is provided, this step has to be done manually. Messy, but I don't yet have a way around it.

   a. Get the storage account connection string:

   ```sh
   azure storage account connectionstring show {storage account name}
   ```

   b. Create a blob container with:

   ```sh
   azure storage container create {container name} -c {connection string from step a.}
   ```

   c. Go to the Web App Application Settings blade and fill out the value of AZURE_STORAGE_CONTAINER with the name of the container and AZURE_STORAGE_ACCOUNT_KEY with the storage account key (obtainable from the Storage Account's Access Keys blade in the Azure UI)

5. Obtain the **Azure Search** key from the service's Keys blade and fill out the Web App Application Settings AZURE_SEARCH_ADMIN_API_KEY value

At this point, you should have an Azure infrastructure ready to host Django, along with support for search, asset storage and SQL as a database backend.