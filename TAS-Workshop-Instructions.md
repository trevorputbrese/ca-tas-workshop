# TAS Workshop Instructions

## Onboarding to TAS

1.  Login to Apps Manager:  https://apps.sys.tanzufordevs.net/
    a.  A separate doc will be shared with your username, password, tenant, and organization.
1.  Access your tenant space. 
2.  Download the `CF CLI`  (https://apps.sys.tanzufordevs.net/tools)
4.  Use the 'CF CLI' to login, targeting your space:
```sh
cf api https://api.sys.tanzufordevs.net
cf login
cf target -o $ORG_NAME
cf target -s $SPACE_NAME"
```



## PART 1: Create a static website and deploy with TAS
1. Create a new directory on your workstation called “static-website”
2. Create an index.html
3. Create a manifest.yaml
4. Create a Staticfile
5. See the following Gist for help https://gist.github.com/p-ssanders/285b81d8e35b380cfb00226b75aedc74
6. Push the app to prod:
```sh
cf push -f manifest.yaml
```
7.  Explore the app via the CLI and UI (Apps Manager)
```
cf app my-static-website
```
8.  Iterate on the app and re-push
9.  Review builfpacks:  what are they and what do they do?  
    a.  https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/staticfile-index.html
10.  Review the concept of "routers":
    a.  https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/deploy-apps-routes-domains.html
11. Review 12 factor logs:
    a.  https://12factor.net/logs
    b.  ```sh
        cf logs my-static-website
        ```

## PART 2:  Deploy "Hello World" DotNet site with TAS
1.  Create a new directory on your workstation:  "dotnet-webapp"
2.  Inside that directory, create a new dotnet blazor app:
```sh
dotnet new blazorserver --no-https -f net6.0 -o .
```
3.  Run the app locally on your workstation:
```sh
dotnet watch
```
4.  Push the app to prod using TAS:
    a.  Create `manifest.yaml` file, but give the app a different name.
    b.  `` cf pufh -f manifest.yaml``
