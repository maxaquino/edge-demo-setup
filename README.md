# edge-demo-setup

The environment is installed on AWS but can be adapted to any infrastructure with little changes.


Playbooks in order of run.

## aap2-setup
Run this playbook to setup the Ansible Automation Platform on OpenShift to be used for the ACM + AAP2 + MICRSOHIFT + EDGE demo

## edge-node-setup
Run this playbook after the aap2-demo-setup playbook to setup and configure the edge node to host microshift and to be imported automagically to RHACM

## import-to-rhacm
A simple playbook to automate the Microshift cluster import on RHACM using Ansible Automation Platform.


## Requirements

- Red Hat Advanced Cluster Management for Kubernetes
- Red Hat Ansible Automation Platform
- One or more EC2 instances to simulate an edge node

The above playbooks should be run on a bastion which has access to the OpenShift instance where Ansible Automation Platform is deployed.

Copy the content of the ec2 instance to files/edge-key.pem file.
It will be used not only to connect to the ec2 instance but to create the edge node credential on ansible too.

A username and password to access RHSM will be required.
It will also be required to have a valid openshift-pull-secret.

If not present on the bastion, please install ansible and awx client
```
sudo dnf install -y ansible

yum-config-manager --add-repo https://releases.ansible.com/ansible-tower/cli/ansible-tower-cli-el8.repo
sudo dnf install -y ansible-tower-cli
```

Install awx cli and the collections required
- sudo yum-config-manager --add-repo https://releases.ansible.com/ansible-tower/cli/ansible-tower-cli-el8.repo
- sudo dnf install -y ansible-tower-cli
- ansible-galaxy collection install -r collections/requirements.yml
- Populate the "hosts" inventory file with the name of the ec2 instance, the user and the ssh key


*Set up environment variables*

These environment variables must be provided before run the playbook

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

*Run the playbook*
```
ansible-playbook aap2-setup.yml

ansible-playbook -i hosts edge-node-setup.yml
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
