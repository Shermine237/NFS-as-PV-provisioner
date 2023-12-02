# How to use NFS as storage classe proviser to bind PVs
## Install NFS-server on file server, Master k8s and Worker k8s
Sample with Archlinux server
```bash
sudo pacman -Sy nfs-utils
```

## Configuration
### Add folder
Add folder to use as mount root
```bash
sudo mkdir /myNFS
```
Add folder in nfs export
```bash
sudo cat "/myNFS *(rw,sync,no_subtree_check)" >> /etc/exports
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
On another PC: 
```bash
mkdir test-folder
sudo mount -t nfs -o vers=4 SERVER-NFS-IP:/myNFS ./test-folder
```




## Install NFS external provisioner on k8s cluster
