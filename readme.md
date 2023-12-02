# How to use NFS as storage classe proviser to bind PVs
## Install NFS-server on file server, Master k8s and Worker k8s
<p>Sample with Archlinux server</p>
<code>sudo pacman -Sy nfs-utils</code>

## Configuration
### Add folder
<p>Add folder to use as mount root</p>
<code>sudo mkdir /myNFS</code><br/>
<p>Add folder in nfs export</p>
<code>sudo cat "/myNFS *(rw,sync,no_subtree_check)" >> /etc/exports</code><br/>
<p>Reload nfs export</p>
<code>sudo exportfs -arv</code>

### Config firewall
<p>Allow nfs port</p>
<code>sudo iptables -A INPUT -p tcp --dport 2049 -j ACCEPT</code><br/>
<code>sudo iptables -A INPUT -p udp --dport 2049 -j ACCEPT</code><br/>
<code>sudo iptables-save > /etc/iptables/iptables.rules </code>

### Test NFS
<p>On another PC: </p>
<code>mkdir test-folder</code><br/>
<code>sudo mount -t nfs -o vers=4 SERVER-NFS-IP:/myNFS ./test-folder</code>




## Install NFS external provisioner on k8s cluster
