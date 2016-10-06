# Keycloak

* Download and install keycloak

```
curl -O https://downloads.jboss.org/keycloak/2.2.1.Final/keycloak-2.2.1.Final.tar.gz
rm -rf keycloak-2.2.1.Final
tar -vxf keycloak-2.2.1.Final.tar.gz
```    

* Start server, import the basicauth realm  & launch it
``` 
cd keycloak-2.2.1.Final/bin
./add-user-keycloak.sh -u admin -p admin -r master
./standalone.sh -Djboss.http.port=8181 -Dkeycloak.migration.action=import -Dkeycloak.migration.provider=singleFile -Dkeycloak.migration.file=/Users/chmoulli/Google-Drive/REDHAT/RH-GP/Presentations/rest-security/scripts/basicauthrealm.json -Dkeycloak.migration.strategy=OVERWRITE_EXISTING
```    

# Open Keycloak console

    open http://localhost:8181/auth
    
# Launch WildFly Swarm server

    mvn clean wildfly-swarm:run
    
# Call the HTTP Endpoint
    
* Pass as parameter the user & password to be used
    
    Tool used is httpie (http//httpie.org)
    
    http -a user:password http://localhost:8080/say/echo?value=hello
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store, must-revalidate
    Connection: keep-alive
    Content-Length: 16
    Content-Type: application/octet-stream
    Date: Wed, 05 Oct 2016 09:35:01 GMT
    Expires: 0
    Pragma: no-cache
    
    Hello Sébastien
    
    http -a user:password http://localhost:8080/say/hello
    
# Openshift
    
* Create Minishift instance
```
    ./scripts/create_minishift.sh
```    
* Configure Docker env variables
```    
    minishift docker-env
    eval $(minishift docker-env)
```    
* Log on to openshift
```    
    oc login -u admin -p admin
```        
* Build Project & run
```
   mvn clean package fabric8:deploy fabric8:log -Dfabric8.mode=kubernetes
```   
* Call the Rest endpoints
```   
   http $(minishift service swarm-rest --url=true)/say/echo?value=hello
   http $(minishift service swarm-rest --url=true)/say/hello
``` 
       
* Deploy Keycloak SSO
```        
    oc create -f http://repo1.maven.org/maven2/io/fabric8/devops/apps/keycloak/2.2.265/keycloak-2.2.265-openshift.yml
```  
* Hack to mount the volume

This command will create the Persistent volumes which are required for Keycloak as the template will create the Persistent Volume Claim

```
gofabric8 volumes
```

Remark: The version 2.2.265 of keycloak doesn't work !!

