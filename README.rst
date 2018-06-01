ceph-ansible
============
Ansible playbooks for Ceph, the distributed filesystem.
Please refer to our hosted documentation here: http://docs.ceph.com/ceph-ansible/stable-3.0/


Configs, to run as default Vagrant demo
============
Environment:
============
# Ubuntu 16.04
# Vagrant 2.1.1
# Ansible v2.3.2.0
apt-get install software-properties-common python-software-properties python-pip
pip install ansible==2.3.2.0
# ceph-ansible 3.0
git clone -b stable-3.0 https://github.com/ceph/ceph-ansible.git


============
Configs added/changed from original ceph-ansible:
============
site.yml
vagrant_variables.yml
group_vars/all.yml
group_vars/osds.yml
group_vars/mons.yml


============
Config for debug:
============
# Run vagrant provision without reboot
vagrant provision 

# ansible debug vars:
- debug:
    msg: "System {{ inventory_hostname }} has uuid {{ ansible_product_uuid }}"

# ansible set vars in Vagrantfile:
ansible.extra_vars = {
      cluster_network: "#{CLUSTER_SUBNET}.0/24",
      journal_size: 100,
      public_network: "#{PUBLIC_SUBNET}.0/24",
      monitor_secret: "AQAPn8tUmPBwCxAAeIfvpDKA1fGvrBeXGdc6xQ==",
      osd_pool_default_pg_num: 128,
  }

============
Manage daemons
============
http://docs.ceph.com/docs/mimic/rados/operations/operating/
  # CEPH status on MON node:
  watch ceph status
  # Show config for service mds
  ceph daemon mds.mds0 config show | less
  # Set debug level for mds
  ceph daemon mds.mds0 config set debug_mds 0/5
  # Logs
  tail -fn50 /var/log/ceph/ceph-mds.mds0.log
  # Restart MDS service
  sudo systemctl stop ceph-mds\*.service ceph-mds.target
  sudo systemctl start ceph-mds\*.service ceph-mds.target


============
Mount cephfs in Ubuntu (client)
============
  apt install ceph-fs-common ceph-common
  mkdir -p /mnt/cephfs/
  mount -t ceph 192.168.42.10:6789:/ /mnt/cephfs/ -o name=admin,secret=AQDFuw9bH4i0IBAAeWbJXZK9vKMT4bCSCgHwAQ==
  umount /mnt/cephfs/
  Where
  192.168.42.10 - public ip of monitor-service
  secret - from file /etc/ceph/ceph.client.admin.keyring on the VM of monitor-service

============
IP-address pools in Vagrant demo
============
CLUSTER_SUBNET (only on OSD VM's)
Network: 192.168.43.0/24
VB network adapter: vboxnet2
VM network adapter: eth2

PUBLIC_SUBNET
Network: 192.168.42.0/24
VB network adapter: vboxnet1
VM network adapter: eth1

NAT & internet
Network: 10.0.2.15/24
VM network adapter: eth0

RGW API
http://192.168.42.50:8080