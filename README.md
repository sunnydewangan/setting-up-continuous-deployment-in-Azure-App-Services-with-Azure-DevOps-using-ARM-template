# Setting up continuous deployment in Azure App Services with Azure DevOps using ARM template

Most of the Git repos hosted on Azure DevOps are not public, they are private Git repos; hence, when you try to configure continuous deployment with Azure DevOps using the ARM template then the deployment fails. In the backend, it fails with a 401 unauthorized error.

The first thing to notice is when we define **"Microsoft.Web/sites/sourcecontrols"** in the template, there is no property named "username" and "password" to pass the credential while passing the Git repoUrl hosted on Azure DevOps. So, we need to find an alternate way wherein the repoUrl should be enough to authenticate and authorize the request.

To solve this problem, we need to use something called "Personal access token" or "PAT" in the URL itself so that our request is authenticated.

Please refer to the following doc to know about PAT and how to generate a PAT key:
https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page

Once you have got your PAT key generated, you can use the repo URL in the following format: https://{PAT}@{org_name}.visualstudio.com/_git/{your_repo}

Here is a sample snippet of the ARM template that you can refer to:

       "resources": [
           {
               "apiVersion": "2018-11-01",
               "name": "web",
               "type": "sourcecontrols",
               "dependsOn": [
                   "[resourceId('Microsoft.Web/sites/', parameters('name'))]"
               ],
            "properties": {
              "repoUrl": "https://{PAT}@{org_name}.visualstudio.com/_git/{your_repo}",
              "branch": "master",
              "isManualIntegration": false
            }
           }
       ]

Hope you find this article useful.

Cheers!!!

