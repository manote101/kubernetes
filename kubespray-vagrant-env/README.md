### VM Environment
![VM-env](https://github.com/manote101/kubernetes/blob/manote-branch/kubespray-vagrant-env/image/4%20Nodes%20Cluster%20-%20Kubespray.png)

![targettend-cluster](https://github.com/manote101/kubernetes/blob/manote-branch/kubespray-vagrant-env/image/4%20Nodes%20Cluster%20Config.png)

### On Ansible Host: prepare necessary tools
```ShellSession
# 1. install python3 
# check whether python3 is available by running 'which python3', if it is not there then run
apt-get install python3 -y

# 2. install pip3
# check whether pip3 is available by running 'which pip3', if it is not there then run
apt-get update
apt-get install python3-pip -y

# 3. Generate SSH key-pair - key-pair file will be saved in ~/.ssh directory
ssh-keygen -N ""

# 4. Copy public key to every hosts 
ssh-copy-id -i ~/.ssh/id_rsa root@172.16.16.101
ssh-copy-id -i ~/.ssh/id_rsa root@172.16.16.102
ssh-copy-id -i ~/.ssh/id_rsa root@172.16.16.103
ssh-copy-id -i ~/.ssh/id_rsa root@172.16.16.104

# 5. Verify that we can use SSH to log-on without password
ssh root@172.16.16.101
ssh root@172.16.16.102
ssh root@172.16.16.103
ssh root@172.16.16.104
```

### Install Kubespray - refer to Kubespray repo, https://github.com/kubernetes-sigs/kubespray
```ShellSession
# Install & Configure K8s by Kubespray
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray

# install required packages (Ansible and its required tools)
pip3 install -r requirements.txt

# Copy ``inventory/sample`` as ``inventory/mycluster``
cp -rfp inventory/sample inventory/mycluster

# Update Ansible inventory file with inventory builder
declare -a IPS=(172.16.16.101 172.16.16.102 172.16.16.103 172.16.16.104)
CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}

# Review and change parameters under ``inventory/mycluster/hosts.yaml``
cat inventory/mycluster/hosts.yaml

# Review and change parameters under ``inventory/mycluster/group_vars``
cat inventory/mycluster/group_vars/all/all.yml
cat inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml

# Deploy Kubespray with Ansible Playbook - run the playbook
ansible-playbook -i inventory/mycluster/hosts.yaml --become --become-user=root cluster.yml

# check whether your K8s cluster is running fine
kubectl version --short
kubectl cluster-info
kubectl get node
```

### How to Add node(s)
```ShellSession
# สมมติต้องการ add node5
# เพิ่มข้อมูลของ new node ลงในไฟล์ inventory/mycluster/hosts.yaml พร้อมระบุ role (i.e. kube-master, kube-node, etcd)
# พร้อมทั้งเตรียมให้สามารถ SSH เข้า node5 ได้โดยไม่ต้องใส่ password
vi inventory/mycluster/hosts.yaml
ssh-copy-id -i ~/.ssh/id_ecdsa root@node5

# run ansible playbook
ansible-playbook -i inventory/mycluster/hosts.yaml --user root scale.yml
```

### How to Remove node(s)
```ShellSession
# run Ansible playboot to remove node
ansible-playbook -i inventory/mycluster/hosts.yaml --user root remove-node.yml --extra-vars "node=node5"   
# ตัวอย่างเป็นการลบ node5, ถ้าต้องการลบมากกว่า  1 node สามารถคั่นด้วยเครื่องหมาย comma
```
