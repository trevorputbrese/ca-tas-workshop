# TAS Workshop Instructions

## NOTE: Getting started
1.  A Windows jumpbox with all the required software pre-installed will be provided to workshop attendees.  The instructor will provide you the credentials and IP address necessary to RDP into your jumpbox.  Included on that jumpbox will be a text file saved on the desktop with additional credentials required for this workshop.  

2.  The instructions below are intended to be run from within the jumpbox.  Please RDP into it before getting started.  

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
1.  Clone the following repo:   https://github.com/p-ssanders/tas-workshop-todo-api  
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
1.  Push the App to TAS:  
    ```
    cf push
    ```
    
    a.  The app will be accessssible at `https://$ROUTE/swagger/index.html`  
    b.  Interact with the API
    c.  Inspect logs:  
    ```
    cf logs
    ```
    d.  Observer failure indicating inability to establish connection to MySQL:
    > MySqlConnector.MySqlException (0x80004005): Unable to connect to any of the specified MySQL hosts.  
    
    e.  Review environmental variables:
    ```
    cf env $APP_NAME
    ```
    f.  observe VCAP_SERVICES without MySQL connection info  

2.  View marketplace services in either AppsMan or the CLI:  
    a.  [AppsMan Marketplace](https://apps.sys.tanzufordevs.net/organizations/4e22e92a-a08a-41ee-a277-4bbdf2c6a753/marketplace)  
    b.  via CLI:  
    ```
    cf marketplace
    ```
    c.  View descriptions of individual plans of MySQL service offering:  
    ```
    cf marketplace -e p.mysql
    ```
3.  Provision a MySQL instance with the `db-small` plan type:  
    ```
    cf create-service p.mysql db-small mysql_$YOUR_NAME
    ```
4.  Wait till the MySQL instance is finished provisioning.  You can check this in `AppsMan` or via the CLI:  
    ```
    cf services
    ```
5.  Once the MySQL service is provisioned, bind it to the app:
    ```
    cf bind-service $APP_NAME $MY_SQL_INSTANCE
    ```  
5.  Restage your app:
    ```
    cf restage $APP_NAME
    ```
6. Interact with the app:  
    a.  Try POST'ing/GET'ing data  
    b.  Inspect logs  
    c.  Observe failure indicating missing schema in the DB:  
    >  “MySqlConnector.MySqlException (0x80004005): Table 'service_instance_db.TodoItems' doesn't exist”  
7.  Run the migrations:
    ```
    cf run-task my-dotnet-api --command "/home/vcap/deps/0/dotnet_publish/todo-api runtask=migrate" --name list-dir
    ```
8.  Check the status of the task:
    ```
    cf tasks $APP_NAME
    ```
9.  Interact with the app again:  
    a.  POST/GET some data  
    b.  Restart the app
    ```
    cf stop $APP_NAME
    cf start $APP_NAME
    ```
    c.  Notice the data persists
10. Scale the app:
    ```
    cf scale $APP_NAME -i 2
    ```
11.  Interact with the app again:  
    a.  Notice the data persists across multiple instances


<br/>

## PART 6:  Stretch Goals for Workshop
1.  Provision an App SSO service
2.  Try a blue/green deployment


<br/>

## PART 7:  Wrap-Up and Q&A


<br/>

## PART 8:  References
https://docs.steeltoe.io/guides/get-to-know-steeltoe/exercise1.html?tabs=visual-studio  
https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-7.0  
https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/concepts-overview.html  
https://12factor.net/  













