# Demonstration setup for OpenShift on Mainframe and other platforms

This demonstrator should work on x86, Power and Z architectures. There are some specific setup procedures that I needed to complete.

## Pre-Requisites
* Clone the repository - if you want to show code changes flowing through in the demo (highly recommeded)
* Two or more environments (x86, Power, Z) if you want to show it across architectures.
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
    --docker-username='<user_name>' \
    --docker-password='<password>' \
    --docker-email='<email>'

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

Download the imagestream and template files (or clone the repo). Then create the image streams and lastly the template
```bash
wget https://raw.githubusercontent.com/yohanswanepoel/django-openshift-js/master/openshift/templates/imagestream-python.json
oc create -f imagestream-python.json

wget https://raw.githubusercontent.com/yohanswanepoel/django-openshift-js/master/openshift/templates/imagestream-postgresql.json
oc create -f imagestream-postgresql.json

wget https://raw.githubusercontent.com/yohanswanepoel/django-openshift-js/master/openshift/templates/django-postgresql-namespace.json
oc create -f django-postgresql-namespace.json

# Verify image stream creation worked
oc get is
## Output
# NAME         IMAGE REPOSITORY                                                       TAGS               UPDATED
# postgresql   image-registry.openshift-image-registry.svc:5000/project1/postgresql   10,12,9.6,latest   9 minutes ago
# python       image-registry.openshift-image-registry.svc:5000/project1/python       2.7,3.6,latest     About a minute ago

# Verify template creation worked
oc get templates
## Output
# NAME                           DESCRIPTION                                                                        PARAMETERS     OBJECTS
# django-psql-no-persist-johan   This template pulls everything from the local namespace. Container based on s...   19 (6 blank)   8

```

## Spin up instance of app
Congratualations! If everything worked up to now, then we are ready to spin up the app. 

I recommend you do this before the demo, you can blow it all away and run it again. Just keep the image streams as that is then cached.

This should be as simple as processing your template
```bash
oc process django-psql-no-persist-demo -P SOURCE_REPOSITORY_URL=<your git repo> | oc create -f -

## check progress with oc get pods


## get your app url
oc get routes | grep django | awk 'http\://{print $2}'
```

You may need to add the route server to /etc/hosts - I will leave that one up to you to figure out

## Demonstrator
Webhook
Steps

## Clean up
I am leaving my initial image streams on purpose. 

```bash
oc delete secret django-psql-example
oc delete service django-psql-example
oc delete route django-psql-example
oc delete is django-psql-example
oc delete bc django-psql-example
oc delete dc django-psql-example
oc delete service postgresql
oc delete dc postgresql
oc delete template django-psql-no-persist-demo
```

## Tips
pull images before demonstration
