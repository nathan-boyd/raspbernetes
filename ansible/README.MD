# rak8s (pronounced rackets - /ˈrækɪts/)

# Prerequisites

## Modify ansible.cfg and inventory

Modify the `inventory` file to suit your environment. Change the names to your liking and the IPs to the addresses of your Raspberry Pis.

If your SSH user on the Raspberry Pis are not the Raspbian default `pi` user modify `remote_user` in the `ansible.cfg`.

## Confirm Ansible is working with your Raspberry Pis:

```
ansible -m ping all
```
This may fail to ping if you have not setup SSH keys and only configured your Pi's with passwords
## Deploy, Deploy, Deploy

```
ansible-playbook cluster.yml
```

# Interact with Kubernetes

## CLI

Test your Kubernetes cluster is up and running:

```
kubectl get nodes
```

The output should look something like this:

```
NAME       STATUS    ROLES     AGE       VERSION
pik8s000   Ready     master    2d        v1.9.1
pik8s001   Ready     <none>    2d        v1.9.1
pik8s002   Ready     <none>    2d        v1.9.1
pik8s003   Ready     <none>    2d        v1.9.1
pik8s005   Ready     <none>    2d        v1.9.1
pik8s004   Ready     <none>    2d        v1.9.1
```

## Dashboard

rak8s installs the non-HTTPS version of the Kubernetes dashboard. This is not recommended for production clusters but, it simplifies the setup. Access the dashboard by running:

```
kubectl proxy
```

Then open a web browser and navigate to:
[http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

# Need to Start Over?

Did something go wrong? Nodes fail some process or not joined to the cluster? Break Docker Versions with apt-update? 

Try the process again from the beginning:

```
ansible-playbook cleanup.yml
```
Wait for everything to run and then start again with:

```
ansible-playbook cluster.yml
```

# Where to Get Help

If you run into any problems please join our welcoming [Discourse](https://discourse.rak8s.io/) community. If you find a bug please open an issue and pull requests are always welcome.

# Etymology

**rak8s** (pronounced rackets - /ˈrækɪts/)

Coined by [Kendrick Coleman](https://github.com/kacole2) on [13 Jan 2018](https://twitter.com/KendrickColeman/status/952242602690129921)

# References & Credits

These playbooks were assembled using a handful of very helpful guides:

* [K8s on (vanilla) Raspbian Lite](https://gist.github.com/alexellis/fdbc90de7691a1b9edb545c17da2d975) by [Alex Ellis](https://www.alexellis.io/)
* [Installing kubeadm](https://kubernetes.io/docs/setup/independent/install-kubeadm/)
* [kubernetes/dashboard - Access control - Admin privileges](https://github.com/kubernetes/dashboard/wiki/Access-control#admin-privileges)
* [Install using the convenience script](https://docs.docker.com/engine/installation/linux/docker-ce/debian/#install-using-the-convenience-script)

A very special thanks to [**Alex Ellis**](https://www.alexellis.io/) and the [OpenFaaS](https://www.openfaas.com/) community for their assitance in answering questions and making sense of some errors.
