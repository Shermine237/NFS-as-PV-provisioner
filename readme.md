# How to use NFS as storage classe proviser to bind PVs
## Install NFS-server on file server, Master k8s and Worker k8s
Sample with Archlinux server, install again in worker k8s and backup-server
```bash
sudo pacman -Sy nfs-utils
```
Enable and start service
```bash
sudo systemctl enable nfs-server.service
sudo systemctl start nfs-server.service
sudo systemctl enable nfsv4-server.service
sudo systemctl start nfs-nfsv4-server.service
```

## Configuration
### Add folder
Add folder to use as mount root
```bash
# create folder
sudo mkdir -p /mnt/kubernetes-volumes
sudo mkdir -p /mnt/kubernetes-volumes/data
# set access
sudo chown -R nobody:nobody /mnt/kubernetes-volumes/data
sudo chmod -R 777 /mnt/kubernetes-volumes/data
```
Add folder in nfs export
```bash
# open /etc/exports and add: /mnt/kubernetes-volumes *(rw,sync,no_subtree_check,insecure,no_root_squash)
```
Reload nfs export
```bash
sudo exportfs -arv
```

### Config firewall
Allow nfs port
```bash
sudo iptables -A INPUT -p tcp --dport 2049 -j ACCEPT
sudo iptables -A INPUT -p udp --dport 2049 -j ACCEPT
sudo iptables-save > /etc/iptables/iptables.rules
```

### Test NFS
```bash
showmount -e SEL-IP-ADDRESS
```
On another PC: 
```bash
mkdir test-folder
sudo mount -t nfs -o vers=4 SERVER-NFS-IP:/mnt/kubernetes-volumes/data ./test-folder
```

## Install nfs-subdir-external-provisioner on k8s cluster
Install with Helm
```bash
# Add nfs-subdir-external-provisioner repos
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner
#  Install in k8s
helm install -n nfs-provisioning --create-namespace nfs-subdir-external-provisioner \
    nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    --set nfs.server=192.168.100.100 \
    --set nfs.path=/mnt/kubernetes-volumes/data \
    --set storageClass.onDelete=retain \
    --set storageClass.pathPattern='/${.PVC.namespace}-${.PVC.name}'
# Verify
kubectl get all -n nfs-provisioning
kubectl get sc -n nfs-provisioning
```
