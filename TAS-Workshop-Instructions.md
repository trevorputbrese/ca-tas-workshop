# TAS Workshop Instructions

## PART 1:  Onboarding to TAS

1.  Login to Apps Manager:   https://apps.sys.tanzufordevs.net <br/>
    a.  A separate doc will be shared with your TAS username, password, tenant org, and tenant space
1.  Access your tenant space. Feel free to poke around.
2.  Download the `CF CLI` from Apps Manager (find it in the tools menu: https://apps.sys.tanzufordevs.net/tools)
4.  Use the 'CF CLI' to login, targeting your space:
    ```sh
    cf api https://api.sys.tanzufordevs.net
    cf login
    cf target -o $ORG_NAME
    cf target -s $SPACE_NAME
    ```
<br/>

## PART 2: Create a static website and deploy with TAS

1. Create a new directory on your workstation called “static-website”<br/>
2. Create an index.html
3. Create a manifest.yaml
4. Create a Staticfile
5. See the following directory for help https://github.com/trevorputbrese/ca-tas-workshop/tree/main/2-static-website
6. Push the app to prod:  
    ```
    cf push -f manifest.yaml
    ```
7.  Explore the app via the CLI and UI (Apps Manager)
    ```
    cf app my-static-website
    ```
8.  Iterate on the app and re-push
9.  Review buildpacks:  what are they and what do they do?  <br/>
    a.  https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/staticfile-index.html
10.  Review the concept of "routes": <br/>
    a.  https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/deploy-apps-routes-domains.html
11. Review 12 factor logs: <br/>
    a.  https://12factor.net/logs <br/>
    b. run:  
    ```
    cf logs my-static-website
    ```

<br/>

## PART 3:  Deploy "Hello World" DotNet Blazor site with TAS
1.  Create a new directory on your workstation:  "dotnet-webapp"
2.  Inside that directory, create a new dotnet blazor app:
    ```
    dotnet new blazorserver --no-https -f net6.0 -o .
    ```
3.  Run the app locally on your workstation:
    ```
    dotnet run
    ```
4.  Push the app to prod using TAS:<br/>
    a.  Create a `manifest.yaml` file, but give the app a different name. <br/>
    b.  run:
    ```
    cf push -f manifest.yaml
    ```
5.  Browse the app at the URL.  
6.  Scale the app via the CLI and/or UI.

<br/>

## PART 4:  Deploy ToDo API Based App & Bind to MySQL instance
1.  Clone https://github.com/p-ssanders/tas-workshop-todo-api  
    ```
    git clone https://github.com/p-ssanders/tas-workshop-todo-api
    ```
2.  Run the application:   
    ```
    cd tas-workshop-todo-api  
    dotnet run
    ```
3.  Observe and explore the API:  
    a.  http://localhost:5180/swagger/index.html  
    b.  POST'ed data can be GET'ed because data is stored in-memory
4.  Checkout branch `mysql` <br/>
    ```
    git checkout mysql  
    ```
    a.  Review changes  
    b.  Review Steeltoe
5.  Run locally:
    ```
    dotnet run  
    ```
    a.  Interact with the API
    b.  Observe connection failure error messages to MySQL  
    c.  Review 12factor [backing services](https://12factor.net/backing-services)  
6.  Start MySQL locall using Docker:  
    ```
    docker run --rm -ti -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=steeltoe -e MYSQL_DATABASE=steeltoe -e MYSQL_USER=steeltoe -e MYSQL_PASSWORD=steeltoe percona:5.7.35
    ```  
    a.  Interact with the API  
    b.  Observe failures indicating missing schema  
7.  Run the DB migration using Steeltoe
    ```
    dotnet run -- runtask=migrate
    ```
    a.  Confirm post and get requests are succesfull
    b.  Review 12 Factor [admin processes](https://12factor.net/admin-processes)


<br/>

## PART 5:  Onto TAS with the ToDo App  
1.  Push the App to TAS





