# Docs
Please see the Github Pages Site for complete documentation: [quarkusrobotshop.github.io](https://quarkusrobotshop.github.io)

Quarkusrobotshop Install
=========

_NOTE:_ Ansible must be installed https://docs.ansible.com/ansible/latest/installation_guide/index.html

The Quarkusrobotshop Ansbile Role performs a basic installation that includes the microservices for a robotshop, installation of the Crunchy PostgreSQL DB, AMQ Streams (Kafka.)


The Quarkusrobotshop Role will deploy an event-driven demo application built with Quarkus, AMQ Streams (Kafka), and MongoDB. The application deploys to OpenShift (Kubernetes.)
The source code for the  [quarkusrobotshop](https://github.com/quarkusrobotshop) application support doc can be found  [here](https://github.com/quarkusrobotshop/quarkusrobotshop-support).



Requirements
------------

* OpenShift 4.14 an up Cluster installed
* Docker or podman

Currently tested on 
-------------------
* OpenShift 4.14.0
* OpenShift Pipelines: 1.9.0
* AMQ Streams: 2.3.0-0odm
* Postgres Operator: v5.3.0
* OpenShift Quay: v3.8.1
* OpenShift GitOps: v1.7.1


Quick Start 
-----------
**Set Environment variables for standard deployment**
> This command will deploy the application on a Single cluster with the following services below. 
* AMQ Streams
* Postgres Operator configuration 
* quarkus robotshop helm deployment
```
$ cat >source.env<<EOF
CLUSTER_DOMAIN_NAME=clustername.example.com
TOKEN=sha256~XXXXXXXXXXXX
ACM_WORKLOADS=n
AMQ_STREAMS=y
CONFIGURE_POSTGRES=y
MONGODB_OPERATOR=n
MONGODB=n
HELM_DEPLOYMENT=y
DELETE_DEPLOYMENT=false
DEBUG=-v
EOF
$ podman run  -it --env-file=./source.env  quay.io/quarkusrobotshop/quarkusrobotshop-ansible:v4.12.1

```



**Set Environment variables for ACM WORKLOADS**
* Gogs server
* OpenShift Pipelines
* OpenShift GitOps
* Quay.io
* AMQ Streams
* Postgres Template deployment
* homeoffice Tekton pipelines
* quarkus-robotshop Tekton pipelines
```
$ cat >source.env<<EOF
CLUSTER_DOMAIN_NAME=clustername.example.com
TOKEN=sha256~XXXXXXXXXXXX
ACM_WORKLOADS=y
AMQ_STREAMS=y
CONFIGURE_POSTGRES=y
HELM_DEPLOYMENT=n
DELETE_DEPLOYMENT=false
DEBUG=-v
EOF
$ podman run  -it --env-file=./source.env  quay.io/quarkusrobotshop/quarkusrobotshop-ansible:v4.12.1
```

**Optional: Change namespace for helm deployments**  
`default is quarkusrobotshop-demo`
```
$ cat >source.env<<EOF
CLUSTER_DOMAIN_NAME=clustername.example.com
TOKEN=sha256~XXXXXXXXXXXX
ACM_WORKLOADS=n
AMQ_STREAMS=y
CONFIGURE_POSTGRES=y
MONGODB_OPERATOR=n
MONGODB=n
HELM_DEPLOYMENT=y
NAMESPACE=changeme
DELETE_DEPLOYMENT=false
DEBUG=-v
EOF
$ podman run  -it --env-file=./source.env  quay.io/quarkusrobotshop/quarkusrobotshop-ansible:v4.12.1
```



ScreenShots
------------------------------------------------
![quarkusrobotshop topology](images/quarkus-robot-applications.png "quarkusrobotshop topology")

![quarkusrobotshop AMQ kafka topics](images/amq-topics.png "quarkusrobotshop  kafka topics")


Usage
----------------
* Default web page 5.0.1-SNAPSHOT  
  * http://quarkusrobotshop-web-quarkus-robot-demo.apps.example.com/
this endpoint is used to view the events coming into the cluster
* Default web page v3.3.1  
  * http://quarkusrobotshop-web-quarkus-robot-demo.apps.example.com/robot
this endpoint is used to view the events coming into the cluster
* If you deploy skip_quarkus_robot_customermock this will automatically push events to the quarkus robot dashboard.
* If you would like to manally push events to AMQ use the command below.
```shell
export ENDPOINT="quarkusrobotshop-web-quarkus-robot-demo.apps.ocp4.example.com"
curl  --request POST http://${ENDPOINT}/order \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
-d '{
    "packages": [
        {
            "item": "CP1FC3_WITH_ROOM",
            "name": "Mickey"
        },
        {
            "item": "CH99A9",
            "name": "Minnie"
        }
    ],
    "customizeOrders": [
        {
            "item": "FAMKD8D8",
            "name": "Mickey"
        },
        {
            "item": "FAC94S3",
            "name": "Minnie"
        }
    ]
}'
```

## Developer Notes
> To develop and modifiy code 
* OpenShift 4.10 an up Cluster installed
* Ansible should be installed on machine
* oc cli must be installed
* Ansible community.kubernetes module must be installed `ansible-galaxy collection install community.kubernetes`
* Install [Helm](https://helm.sh/docs/intro/install/) Binary
* [Postges Operator](https://github.com/tosin2013/postgres-operator) for Quarkus robotShop 5.0.1-SNAPSHOT Deployments
* pip3 


Role Variables
--------------
Type  | Description  | Default Value
--|---|--
deployment_method | docker or s2i build | docker
skip_amq_install |  Skip Red Hat AMQ Install  |  false
skip_mongodb_operator_install |  Skip MongoDB Operator Install  |  false
single_mongodb_install | Skip single instance mongodb | false
skip_quarkusrobotshop_helm_install |  Skip quarkusrobotshop helm chart install  |  false
openshift_token | OpenShift login token  | 123456789
openshift_url | OpenShift target url  | https://master.example.com
project_namespace | OpenShift Project name for the quarkus-robot | quarkus-robot-demo
insecure_skip_tls_verify  |  Skip insecure tls verify  |  true
default_owner | Default owner of template files. | root
default_group | Default group of template files. |  root
delete_deployment  | delete the deployment and project for quarkus-robot-demo  | false
amqstartingCSV  | Red Hat AMQ csv version  |  amqstreams.v1.6.1
mongodbstartingCSV  | MongoDB Ops Manager version  |  mongodb-enterprise.v1.8.0
config_location  | default location for application templates  | "/tmp/"
version_package | Default container package tag | 5.0.0-SNAPSHOT
version_counter | Default container counter tag | 5.0.1-SNAPSHOT
version_customermocker | Default container customermocker tag | 3.0.1
version_customize | Default container customize tag | 5.0.0-SNAPSHOT
version_web | Default container web tag | 5.0.1-SNAPSHOT
helm_chart_version | Version of Qaurkus Cafe Helm Chart | 3.4.4
pgsql_username | Default postgress user  | coffeshopadmin
postgres_password | this is the postgress password that will be used in deployment| must be changed
pgsql_url | default postgres URL | 'jdbc:postgresql://robotshopdb:5432/robotshopdb?currentSchema=robotshop'
storeid | Store id for web frontend | RALEIGH
quarkus_log_level | Quarkus robot shop log level |  INFO
quarkusrobotshop_log_level | Microservice log level | DEBUG

**Download the deploy-quarkusrobotshop-ansible.sh shell script**
```
$ curl -OL https://raw.githubusercontent.com/quarkusrobotshop/quarkusrobotshop-ansible/master/files/deploy-quarkusrobotshop-ansible.sh
$ chmod +x deploy-quarkusrobotshop-ansible.sh
$ ./deploy-quarkusrobotshop-ansible.sh -d ocp4.example.com -t sha-123456789 -p 123456789 -s ATLANTA
```

**To Build container image**
``` 
podman build -t  quarkusrobotshop-ansible:v0.0.2 -f Dockerfile
```

**Test Container**
```
podman run  -it   --env-file=./source.env  quarkusrobotshop-ansible:v0.0.2 bash or
podman run  -it --env-file=./source.env   localhost/quarkusrobotshop-ansible:v0.0.2
```

**Delete old containers**
```
podman rm $(podman ps -a | grep Exited | awk '{print $1}')
podman rmi localhost/quarkusrobotshop-ansible:v0.0.2 
```

Troubleshooting
---------------
Force delete kafka crds after bad install
```
oc get crds -o name | grep '.*\.strimzi\.io' | xargs -r -n 1 oc delete
```

To-Do
-------
* Ansible k8s – Manage Kubernetes (K8s) objects deployment example


License
-------

GPLv3

Author Information
------------------

This role was created in 2020 by [Tosin Akinosho](https://github.com/tosin2013)


