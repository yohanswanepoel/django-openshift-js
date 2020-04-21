# Demonstration setup for OpenShift on Mainframe and other platforms

This demonstrator should work on x86, Power and Z architectures. There are some specific setup procedures that I needed to complete.

## Pre-Requisites
* Ability to pull images form Red Hat for multiplatform use cases.
* VSCode, Theia or CRC to do code change
* Git Access
* OC client tools for setup
* Curl client to kick off multiple builds is useful

This demo does require PVCs, it uses ephemeral storage for Postgresql to keep things simple.

### Ability to pull images from Red Hat
On certain setups you may not have have a cluster that can connect to the Red Hat Registry. The easiest way to test this and set this up is using the console.

If you can use the Samples operator then this simplifies things. https://docs.openshift.com/container-platform/4.2/openshift_images/configuring-samples-operator.html

I did not have access to it on my setup. You will know if you have it as the Developer Catalogue will have the images you need ready. E.g. PostgreSQL and Python.

To test if you can pull images run the following. You need a Red Hat username and password. You could head to developer.redhat.com for this and also login using oc command.
```bash

oc project <your project>

oc create secret docker-registry demopullsecret \
    --docker-server=registry.redhat.io \
    --docker-username=<user_name> \
    --docker-password=<password> \
    --docker-email=<email>

## Useful for debugging
oc get secret demopullsecret --output="jsonpath={.data.\.dockerconfigjson}" | base64 --decode


oc secrets link default demopullsecret --for=pull

oc secrets link builder demopullsecret
```

You should now be able to pull the following images:
* registry.redhat.io/rhscl/python-36-rhel7:latest
* registry.redhat.io/rhel8/postgresql-96:latest

## Setup Image streams - if they are not in the openshift namespace
The assumption here is that you cannot access the images in the openshift namespace and that they are not there.




## Setup Template


## Demonstrator
Webhook
Steps

## Clean up

## Tips
pull images before demonstration
