# tungsten-openstack
How to install Tungsten Fabric SDN with Openstack.

Basic instruction, all-in-one example below. Hosted OS is CentOS Linux release 7.9.2009 (Core)

* Install required packages
```bash
sudo su
yum -y install epel-release
yum -y install git screen wget tcpdump
```
* Install pip for Python 2.7
```bash
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
chmod a+x get-pip.py
./get-pip.py
```
* Install ansible 2.7.18
```bash
pip install ansible==2.7.18
```
* Clone ansible-deployer reposotory
```bash
git clone http://github.com/tungstenfabric/tf-ansible-deployer /opt/tf/tf-ansible-deployer
cd /opt/tf/tf-ansible-deployer
```
* Generate keypair to run ansible locally
```bash
ssh-keygen -b 2048 -t rsa -f /root/.ssh/id_rsa -q -N ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
* Create deployment configuration /opt/tf/tf-ansible-deployer/config/instances.yaml
```
provider_config:
  bms:
    ssh_user: root
    ssh_public_key: /root/.ssh/id_rsa.pub
    ssh_private_key: /root/.ssh/id_rsa
    ntpserver: pool.ntp.org
instances:
  server01:
    provider: bms
    ip: $IP
    roles:
      config_database:
      config:
      control:
      analytics_database:
      analytics:
      webui:
      vrouter:
      openstack:
      openstack_compute:
global_configuration:
contrail_configuration:
  CONTRAIL_VERSION: latest
  UPGRADE_KERNEL: no
kolla_config:
  kolla_globals:
    enable_haproxy: no
    enable_ironic: no
    enable_swift: no
```
* Run playbooks
```bash
ansible-playbook -vvv -e orchestrator=openstack -i inventory/ playbooks/configure_instances.yml
ansible-playbook -vvv -i inventory/ playbooks/install_openstack.yml
ansible-playbook -vvv -e orchestrator=openstack -i inventory/ playbooks/install_contrail.yml
```
