### SEAFILE OPEN API

1. set api on github settings  
   https://dash.readme.com/project/seafile-api/v1.0/api-key  
   https://github.com/haiwen/seafile-open-api/settings
   ![readmeio-apikey.png](img%2Freadmeio-apikey.png)
   ![github-setting.png](img%2Fgithub-setting.png)
2. Syncing Docs via CLI / GitHub  
   https://docs.readme.com/main/docs/rdme
3. use rdme command tool to generate github workflows for openapi file  
   install rdme then login and run  
   ```
   npm install -g rdme
   rdme login
   rdme openapi ./api/xxxxx.yaml
   ```
   it will generate another workflow, cut and copy to rdme.yaml
4. create NEW CATEGORY on ADMIN PANEL for reference file  
   ![new-category.png](img%2Fnew-category.png)
   ![getcategories.png](img%2Fgetcategories.png)
   then set YAML front matter block like this:  
   ![yaml-front.png](img%2Fyaml-front.png)
   also need set docs version:  
   ![docs-version.png](img%2Fdocs-version.png)
5. set branch of github workflows  
   set version of openapi version  
   up to the current readme version  
   ![api-version.png](img%2Fapi-version.png)
   ![github-workflow-branch.png](img%2Fgithub-workflow-branch.png)
   ![readme-version.png](img%2Freadme-version.png)  
   keep branch and api version consistent  
