### VM Environment
![VM-env](https://github.com/manote101/kubernetes/blob/manote-branch/kubespray-vagrant-env/image/4%20Nodes%20Cluster%20-%20Kubespray.png)

![targettend-cluster](https://github.com/manote101/kubernetes/blob/manote-branch/kubespray-vagrant-env/image/4%20Nodes%20Cluster%20Config.png)

### Prepare you Ansible Host
```

```

### How to Add node(s)
From Ansible Host (callde node5):
- Copy SSH key from Ansible host to node5
```sh
ssh-copy-id -i ~/.ssh/id_rsa root@172.16.16.105
```
- เพิ่มข้อมูลของ new node ลงในไฟล์ inventory/mycluster/hosts.yml พร้อมระบุ role (i.e. kube-master, kube-node, etcd)
- run ansible playbook
```sh
ansible-playbook -i inventory/mycluster/hosts.yml --user root scale.yml
```

### How to Remove node(s)
 run Ansible playbook
```sh
ansible-playbook -i inventory/mycluster/hosts.yml --user root remove-node.yml --extra-vars "node=node5"   
# ตัวอย่างเป็นการลบ node5, ถ้าต้องการลบมากกว่า  1 node สามารถคั่นด้วยเครื่องหมาย comma
```
