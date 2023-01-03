# edge-demo-setup

The environment is installed on AWS but can be adapted to any infrastructure with little changes.

The below playbooks, in order of run, should be run on a bastion which has access to the OpenShift instance where Ansible Automation Platform is deployed.

## aap2-setup
Run this playbook first to setup the Ansible Automation Platform on OpenShift to be used for the ACM + AAP2 + MICRSOHIFT + EDGE demo

## edge-node-setup
Run this playbook to setup and configure the edge node to host microshift and to be imported automagically to RHACM using the ansible callback feature.

## import-to-rhacm
This playbook will be invoked by the edge node in order to import Microshift to RHACM using Ansible Automation Platform.


## Requirements

- Red Hat Advanced Cluster Management for Kubernetes
- Red Hat Ansible Automation Platform
- A token to connect to RHACM
- A token to connect to Ansible Automation Platform
- One or more EC2 instances to simulate edge nodes

Copy the content of the ec2 instance's ssh key created to **files/edge-key.pem** file.

It will be used either to connect to the ec2 instance(s) and to create the edge node(s) credential on AAP.

A username and password to access RHSM (Red Hat Subscription Manager) will be required.

It will also be required to have a valid **openshift-pull-secret**.


If not present on the bastion, please install ansible, awx client and collections required
```
sudo dnf install -y ansible

sudo yum-config-manager --add-repo https://releases.ansible.com/ansible-tower/cli/ansible-tower-cli-el8.repo
sudo dnf install -y ansible-tower-cli
ansible-galaxy collection install -r collections/requirements.yml
```

Populate the **hosts** inventory file accordingly with the name of the ec2 instance, the user and the ssh key.


*Set up environment variables*

These environment variables must be provided before run the **aap2-setup** playbook

```
export CONTROLLER_HOST=<ansible automation controller URL>
export CONTROLLER_USERNAME=<username>
export CONTROLLER_PASSWORD=<password>
export CONTROLLER_VERIFY_SSL=no
//export CONTROLLER_TOKEN=`awx login | jq -r .token`
export CONTROLLER_TOKEN=<ansible admin token>
export CONTROLLER_HOST_CONFIG_KEY=<key for provisioning callback>

//export K8S_AUTH_KUBECONFIG=<path to kubeconfig>

export RHACM_API=<rhacm url>
export RHACM_TOKEN=<token to access rhacm>
```

*Run the playbooks below from the bastion*
```
ansible-playbook aap2-setup.yml

ansible-playbook -i hosts edge-node-setup.yml

No need to run the third script
```

## AAP Credentials Type for RHACM
The login information for Red Hat Advanced Cluster Management  must be saved in Tower using a custom credentials type defined as follows.

*INPUT CONFIGURATION:*

```yaml
fields:
  - id: api_endpoint
    type: string
    label: API Endpoint
  - id: api_token
    type: string
    label: API Token
    secret: true
required:
  - api_endpoint
  - api_token
```


*INJECTOR CONFIGURATION:*

```yaml
extra_vars:
  rhacm_api: '{{ api_endpoint }}'
  rhacm_token: '{{ api_token }}'
```

## AAP Credentials Type for RHACM
The login information for Red Hat Advanced Cluster Management  must be saved in Tower using a custom credentials type defined as follows.

*INPUT CONFIGURATION:*

```yaml
fields:
  - id: api_endpoint
    type: string
    label: API Endpoint
  - id: api_token
    type: string
    label: API Token
    secret: true
required:
  - api_endpoint
  - api_token
```


*INJECTOR CONFIGURATION:*

```yaml
extra_vars:
  rhacm_api: '{{ api_endpoint }}'
  rhacm_token: '{{ api_token }}'
```