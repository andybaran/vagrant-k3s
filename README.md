# Vagrant K3's

This is intended to get a 3 node k3's enviroment up and running with your locally installed hypervisor of choice. The vagrantfile is "stolen" and modified from [here](https://github.com/michaelc0n/k3s).  It has been tested with VirtualBox and Hyper-V.  Hyper-V networking will behave differently with Vagrant however so it is important to be aware of [this](https://www.vagrantup.com/docs/hyperv/limitations.html) caveat.
   
1. Install kubectl for your OS
2. Install vagrant for your OS
3. Clone this repo and CD to its root level folder
4. Run `vagrant up`
5. Per [this article](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) we need to follow the instructions0[here](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md) to create a serivce account for the dashboard
   1. Run `kubectl apply -f ./k8s-dashboard/dashboard-adminuser.yaml` to create a ServiceAccount user in the kubernetes-dashboard namespace called admin-user
   2. Run `kubectl apply -f ./k8s-dashboard/dashboard-adminuser.yaml` to bind the ServiceAccount you just created to the cluster-admin role across the whole environment.  You're giving this account a lot more permissions than it needs; **DON'T DO THIS IN PRODUCTION**.  Some reference [docs](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#default-roles-and-role-bindings).
6. Run `kubectl proxy` and open http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/ to get to your dashboard.1

# Troubleshooting Links

1. [Dashboard GUI troubleshooting with kube proxy and discussions around just exposing the dashboard via a LB](https://github.com/rancher/k3s/issues/233)
2. Running into disk space issues? Try change your VAGRANT_HOME environment variable, starting a new console and running vagrant up again.
   1. Windows : setx VAGRANT_HOME "E:\Vagrant" /M
   2. Other : export VAGRANT_HOME=X:\PATH\TO\VAGRANT
3. [awk regexp](https://www.gnu.org/software/gawk/manual/html_node/Regexp.html)