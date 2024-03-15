# Notes on Legacy deployments

Quarkus CoffeeShop v3.3.1 and lower dependencies
-----------
```
ansible-galaxy collection install community.kubernetes

# Install Helm 
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

Quarkus CoffeeShop v3.3.1 OpenShift Deployment  with MongoDB Operator
-----------------------------
```
$ ansible-galaxy install tosin2013.quarkus_robot_demo_role
$ export DOMAIN=ocp4.example.com
$ export OCP_TOKEN=123456789
$ cat >deploy-quarkus-robot.yml<<YAML
- hosts: localhost
  become: yes
  vars:
    openshift_token: ${OCP_TOKEN}
    openshift_url: https://api.${DOMAIN}:6443
    insecure_skip_tls_verify: true
    default_owner: ${USER}
    default_group: ${USER}
    project_namespace: quarkusrobotshop-demo
    delete_deployment: false
    skip_amq_install: false
    skip_configure_postgres: true
    skip_mongodb_operator_install: false
    skip_quarkusrobotshop_helm_install: false
    domain: ${DOMAIN}
    helm_chart_version: 3.3.0
    version_package: 3.0.0
    version_counter: 3.1.0
    version_customermocker: 3.0.1
    version_customize: 3.1.0
    version_web: 3.1.0
  roles:
    - tosin2013.quarkus_robot_demo_role
YAML
$ ansible-playbook  deploy-quarkus-robot.yml
```

Quarkus CoffeeShop v3.3.1 OpenShift Deployment  with single mongoDB instance
-----------------------------
```
$ ansible-galaxy install tosin2013.quarkus_robot_demo_role
$ export DOMAIN=ocp4.example.com
$ export OCP_TOKEN=123456789
$ cat >deploy-quarkus-robot.yml<<YAML
- hosts: localhost
  become: yes
  vars:
    openshift_token: ${OCP_TOKEN}
    openshift_url: https://api.${DOMAIN}:6443
    insecure_skip_tls_verify: true
    default_owner: ${USER}
    default_group: ${USER}
    project_namespace: quarkusrobotshop-demo
    delete_deployment: false
    skip_amq_install: false
    skip_configure_postgres: true
    skip_mongodb_operator_install: true
    single_mongodb_install: true
    skip_quarkusrobotshop_helm_install: false
    domain: ${DOMAIN}
    helm_chart_version: 3.3.0
    version_package: 3.0.0
    version_counter: 3.1.0
    version_customermocker: 3.0.1
    version_customize: 3.1.0
    version_web: 3.1.0
  roles:
    - tosin2013.quarkus_robot_demo_role
YAML
$ ansible-playbook  deploy-quarkus-robot.yml
```