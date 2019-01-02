# raspbernetes

## Hardware

### Print List
* 1 Top, 4 Carts from [Raspberry Pi Rack Case](https://www.thingiverse.com/thing:2795195)
* 4 Middles from [Raspberry Pi ](https://www.thingiverse.com/thing:3224837)

### Purchase List
* 4 [ELEMENT Element14 Raspberry Pi 3 B+](https://www.amazon.com/gp/product/B07BDR5PDW/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1)  
* 1 [NETGEAR 5-Port Gigabit Ethernet Unmanaged Switch](https://www.amazon.com/gp/product/B00QR6XFHQ/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1)
* 4 [SanDisk 32GB Ultra microSDXC UHS-I Memory Card](https://www.amazon.com/gp/product/B073JWXGNT/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)
* 1 [Anker PowerPort 6 (60W 6-Port USB Charging Hub)](https://www.amazon.com/gp/product/B00WI2DN4S/ref=oh_aui_detailpage_o08_s00?ie=UTF8&psc=1)
* 6 [Cat 6 Ethernet Cable 1 ft Black - Flat Internet Network Cable](https://www.amazon.com/gp/product/B01IQWGKQ6/ref=oh_aui_detailpage_o08_s00?ie=UTF8&psc=1)
* 1 package [Yootop 20Pcs Brass Pillar Spacers Male to Female Thread Hex Standoff Nut M4X25mm+6mm](https://www.amazon.com/gp/product/B07GWFLZMM/ref=oh_aui_detailpage_o09_s00?ie=UTF8&psc=1)

## Software

Download [Raspbian](https://www.raspberrypi.org/downloads/raspbian/)

Flash with [Etcher](https://www.balena.io/etcher/)

Enable SSH with 

```sh
touch /Volumes/boot/ssh
```

Eject the disk volume
```sh
sudo umount /Volumes/boot
```

Add ssh keys
```
ansible-playbook -i inventory auth_keys.yml --ask-pass --extra-vars='pubkey="$(echo ~/.ssh/id_ras.pub)" username="username"'
```

Run ansible script
```sh
ansible-playbook cluster.yml --extra-vars "password=NEW_PASSWORD kube_dest=PATH_TO_LOCAL_KUBECONFIG"
```

ADD KubeConfig Env Var
```sh
export KUBECONFIG=$HOME/.kube/config:raspbernetes.conf
```

List K8s Nodes
```sh
kubectl config use-context kubernetes
kubectl get nodes
```

Proxy Dashboard
```
kubectl proxy
```

Get token for dashboard
```
kubectl -n kube-system describe secrets \
   `kubectl -n kube-system get secrets | awk '/clusterrole-aggregation-controller/ {print $1}'` \
       | awk '/token:/ {print $2}'
```

Open Dashboard in browser: [Dashboard](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login)

Scratch

### Helm
On Master
- install helm binary
- add raspi tiller with 
```
helm init --tiller-image=jessestuart/tiller:v2.9.1
```

```
# not for prod (binds sys account to helm)
kubectl create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default
```

### Setup NFS Server on PI

#### Fix Locale Setting 
``` 
$ locale-gen en_US.UTF-8
```

```
sudo apt-get install nfs-kernel-server nfs-common

sudo systemctl enable nfs-kernel-server

# Add following like to /etc/exports
/mnt/extssd1/kube/ 192.168.1.*(rw,sync,no_subtree_check,no_root_squash)
sudo exportfs -a
```

install on controller
$ helm install stable/nfs-client-provisioner --set nfs.server=x.x.x.x --set nfs.path=/exported/path

