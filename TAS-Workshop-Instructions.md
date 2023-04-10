#TAS Workshop Instructions

##Onboarding to TAS
1.  Login to Apps Manager:  https://apps.sys.tanzufordevs.net/
1.  Access your tenant space.  This file here lists the space to which you have been assigned.
2.  Download the `CF CLI`  (https://apps.sys.tanzufordevs.net/tools)
4.  Use the 'CF CLI' to login, targeting your space:
```sh
cf api https://api.sys.tanzufordevs.net
cf login
cf target -o $ORG_NAME
cf target -s $SPACE_NAME"
```