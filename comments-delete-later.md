# notes

**Prerequisite/SQLServer**

Add instructions to clone the repo and cd into the repo

In case someone uses a different namespace, I suggest you Remove namespace from

```
openshift/sql-server/pvc.yaml

openshift/sql-server/Deployment.yaml
```

if you want to specify namespace
```
oc create -f openshift/sql-server/pvc.yaml -n $namespace
oc create -f openshift/sql-server/Deployment.yaml $namespace
```

oc expose deployment/sqlserver -n dotnet —> We can also create a Service.yaml
```
kind: Service
apiVersion: v1
metadata:
  name: sqlserver
spec:
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
  type: ClusterIP
  selector:
    app: sqlserver
```
**s2i**

*PublicApi*

  ```  oc create -f openshift/publicApi/configmap.yaml```

remove namespace from openshift/publicApi/configmap.yaml

also another way to create the config map would
```
oc create configmap appsettings-cm \
    --from-file=appsettings.json=src/PublicApi/appsettings.Openshift.json
```
oc new-app may fail if dotnet:7-0-ubi8 doesn’t exist as an image stream
```
oc import-image dotnet:7.0-ubi8 --from=registry.redhat.io/rhel8/dotnet-70:7.0-12 --confirm
```
to track status of build, add
```
oc logs -f buildconfig/public-api
```
set volume is failing change to
```
 oc set volume deployment/public-api --add --name appsettings-vol --mount-path /opt/app-root/app/appsettings.json --configmap-name=appsettings-cm --sub-path=appsettings.json
```
*WebApp*

to track status of build, add

```
oc logs -f buildconfig/web-app
```

set volume is failing change to
```
  oc set volume deployment/web-app --add --name appsettings-vol --mount-path /opt/app-root/app/appsettings.json --configmap-name=appsettings-cm --sub-path=appsettings.json
```
for both deployments, you can set the volume before tracking the status of the build

**Docker**

only has instructions for publicAPI

also no environment variables for the database
