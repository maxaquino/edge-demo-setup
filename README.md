# edge-demo-setup

## aap2-setup (playbook)
Run this playbook to setup the Ansible Automation Platform on OpenShift to be used for the ACM + AAP2 + MICRSOHIFT + EDGE demo
The environment is installed on AWS but can be adapted to any infrastructure with little changes.

## edge-node-setup (playbook)
Run this playbook after the aap2-demo-setup playbook to setup and configure the edge node to host microshift and to be imported automagically to RHACM

## Requirements

The above playbooks should be run on a bastion which has access to the OpenShift instance where Ansible Automation Platform is deployed.

Copy the content of the ec2 instance to files/edge-key.pem file.
It will be used not only to connect to the ec2 instance but to create the edge node credential on ansible too.

A username and password to access RHSM will be required.
It will also be required to have a valid openshift-pull-secret.

If not present on the bastion, please install ansible and awx client
```
sudo dnf install -y ansible

yum-config-manager --add-repo https://releases.ansible.com/ansible-tower/cli/ansible-tower-cli-el8.repo
dnf install ansible-tower-cli
```

Install awx cli and the collections required
- yum-config-manager --add-repo https://releases.ansible.com/ansible-tower/cli/ansible-tower-cli-el8.repo
- dnf install ansible-tower-cli
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
