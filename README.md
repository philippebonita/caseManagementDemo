# caseManagementDemo

This repository provides all elements to build and deploy the Case Management demo (_Online shopping fraud use-case_)

it is compatible with Bonita **7.7.0** and BCD **2.0.x**. 

this readme explains how to configure and deploy this demo with BCD on a *local* server (not on AWS for the moment).

## eventHandler
This implementation requires an event handler described in the RestAPIExtension _caseEventHandler_. 

This event handler is automatically deployed on the target platform when launching the maven build command. 
it requires specific dependencies which are not available in the maven sp repo 7.7.0 (should be integrated into the maven sp repo from 7.8). 
for 7.7.0 it must be get from _repositories.rd.lan_ server (added at the end of the end of the pom.xml) : **you must ensure to have access to the machine _repositories.rd.lan_**

#### configuration for event handler auto-deployment at build

* edit the pom.xml of RestAPIExtension _caseEventHandler_
* change the property `<target.db.path>` value to link to the h2 database 
* change the property `<outputDirectory>` of execution `<id>copy-handler-to-studio-tomcat</id>` to point to the tomcat WEB-INF

## deploy this demo with BCD

First install BCD with 7.7.0 dependencies.
You must have a blank Bonita Runtime ready to be used (you should install the usual tomcat bundle)

In BCD controller:
* `cd bonita-continuous-delivery`
* clone this git repo : `git clone <this repo name>`
* modify the scenario build_and_deploy to set the correct bonita version and give the target server local url
  * `cp scenarios/build_and_deploy.ym.EXAMPLE scenarios/build_and_deploy.yml`
  * `vi scenarios/build_and_deploy.yml`
  * set the _bonita_version_ to 7.7.0 and save the file
  * define the url to the local Bonita Runtime :
    * `vi ansible/inventory/static-inventory/livingapp-build-deploy-static-inventory`
    * set the _bonita_url_ to the local Bonita runtime (ex bonita_url=http://192.168.1.100:8080/bonita)
* build the repo `bcd -y -s scenarios/build_and_deploy.yml livingapp build -e Demo -p <this repo name>/`
  * it will build all artifacts and deploy the event handler on the target server
* deploy the repo `bcd -y -s scenarios/build_and_deploy.yml livingapp deploy -p <this repo name>/target/<this repo name>`
  * it will deploy all artifacts for Case Management demo
 
 ## Demo 

the fraud app is available on http://<target server>/bonita/apps/cases/home/
* in account you should enter an account starting with '1234'
* it will list all transactions of the customer
* then enter any ID (identifier of the fraud Case) and Type (type of fraud)
* a new case appear on the Case list and can be opened to be managed
 
 


