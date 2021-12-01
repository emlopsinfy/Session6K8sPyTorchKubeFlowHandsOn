

We first need to [install/deploy (Links to an external site.)](https://www.kubeflow.org/docs/components/pipelines/installation/) KubeFlow. Before we do that, we need to make sure we have kubernetes and kubectl installed. 

Go to Local Deployment.

In Local Deployment, followed below.

## Deploying Kubeflow Pipelines

The installation process for Kubeflow Pipelines is the same for all three environments covered in this guide: kind, K3s, and K3ai.

**Note**: Process Namespace Sharing (PNS) is not mature in Argo yet - for more information go to [Argo Executors](https://argoproj.github.io/argo-workflows/workflow-executors/) and reference “pns executors” in any issue you may come across when using PNS.

1. To deploy the Kubeflow Pipelines, run the following commands:

   ```shell
   # env/platform-agnostic-pns hasn't been publically released, so you will install it from master
   export PIPELINE_VERSION=1.7.1
   kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=$PIPELINE_VERSION"
   kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io
   kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/platform-agnostic-pns?ref=$PIPELINE_VERSION"
   ```

   The Kubeflow Pipelines deployment may take several minutes to complete.

   First error - export is not recognized.

   So used ‘set’ instead export.

2. Verify that the Kubeflow Pipelines UI is accessible by port-forwarding:

   ```shell
   kubectl port-forward -n kubeflow svc/ml-pipeline-ui 8080:80
   ```

   Then, open the Kubeflow Pipelines UI at `http://localhost:8080/` or - if you are using kind or K3s within a virtual machine - `http://{YOUR_VM_IP_ADDRESS}:8080/`

   Note that K3ai will automatically print the URL for the web UI at the end of the installation process.

   **Note**: `kubectl apply -k` accepts local paths and paths that are formatted as [hashicorp/go-getter URLs](https://github.com/kubernetes-sigs/kustomize/blob/master/examples/remoteBuild.md#url-format). While the paths in the preceding commands look like URLs, they are not valid URLs.

##### UNABLE TO INSTALL IN GIT BASH WINDOWS

##### Downloading UBUNTU

https://ubuntu.com/wsl

And Download from the Microsoft Store.

Ubuntu on Windows allows you to use Ubuntu Terminal and run Ubuntu command line utilities including bash, ssh, git, apt and many more.

Please note that Windows 10 S does not support running this app.

To launch, use "ubuntu" on the command-line prompt (cmd.exe), or click on the Ubuntu tile in the Start Menu.

To use this feature, one first needs to use "Turn Windows features on or off" and select "Windows Subsystem for Linux", click OK, reboot, and use this app.

The above step can also be performed using Administrator PowerShell prompt:
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux

This app installs the Ubuntu 20.04 LTS release on Windows.

Please note that app updates don't change the Ubuntu installation. To upgrade to a new release please run do-release-upgrade in the Ubuntu Terminal.

For more information about Windows Subsystem for Linux please visit: https://wiki.ubuntu.com/WSL



##### I have installed Ubuntu App, username and password is ‘raajesh’

Below are the steps followed.

##### 1- 

raajesh@LAPTOP-UN5K40HI:~$ docker --version

The command 'docker' could not be found in this WSL 1 distro.
We recommend to convert this distro to WSL 2 and activate
the WSL integration in Docker Desktop settings.

For details about using Docker Desktop with WSL 2, visit:

https://docs.docker.com/go/wsl2/

##### 2-

check first - wsl -l -v

it returned 1 for Ubuntu.

So I went to powershell and given the command below.

PS C:\WINDOWS\system32> wsl.exe --set-version Ubuntu 2
Conversion in progress, this may take a few minutes...
For information on key differences with WSL 2 please visit https://aka.ms/wsl2
Conversion complete.

##### 3-

Again in Ubuntu App.

raajesh@LAPTOP-UN5K40HI:~$ docker --version

The command 'docker' could not be found in this WSL 2 distro.
We recommend to activate the WSL integration in Docker Desktop settings.

For details about using Docker Desktop with WSL 2, visit:

https://docs.docker.com/go/wsl2/

##### 4-

Docker Desktop Settings then Resources then WSL Integration then Enable Ubuntu.

Apply and Restart.

##### 5-

Tried minikube start from CMD, then tried kubectl get nodes in Ubuntu terminal, its not working.

##### 6-

Since minikube gives errors, trying to install k3s from Ubuntu terminal now.

given below command

##### curl -sfL https://get.k3s.io | sh -

https://www.youtube.com/watch?v=O3s3YoPesKs

Not working.

Finding wsl ip.

Ubuntu Terminal : ip addr | grep eth0

https://docs.microsoft.com/en-us/windows/wsl/networking

raajesh@LAPTOP-UN5K40HI:~$ ip addr | grep eth0
6: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
​    inet 172.28.57.32/20 brd 172.28.63.255 scope global eth0
13: veth0a3f4c66@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default
raajesh@LAPTOP-UN5K40HI:~$

##### started following below official doc

https://k3s.io/

```
curl -sfL https://get.k3s.io | sh -
# Check for Ready node,
takes maybe 30 seconds
k3s kubectl get node
```

curl command dint work, but k3s kubectl get node worked.

##### 1. Download K3s - [latest release](https://github.com/rancher/k3s/releases/latest), x86_64, ARMv7, and ARM64 are supported 2. Run server

```
sudo k3s server &
# Kubeconfig is written to /etc/rancher/k3s/k3s.yaml
sudo k3s kubectl get node

# On a different node run the below. NODE_TOKEN comes from /var/lib/rancher/k3s/server/node-token
# on your server
sudo k3s agent --server https://myserver:6443 --token ${NODE_TOKEN}
```



ran ‘sudo k3s server &’

get the token by running this command :

raajesh@LAPTOP-UN5K40HI:~$ sudo cat /var/lib/rancher/k3s/server/node-token
K108211e20657f883ecc1c38126a427f166341a0f1a8e1a20501a7df29fc9703f65::server:10997ae53b9103160ebf29f08cb944a5
raajesh@LAPTOP-UN5K40HI:~$

then run that token command

raajesh@LAPTOP-UN5K40HI:~$ sudo k3s agent --server https://myserver:6443 --token K108211e20657f883ecc1c38126a427f166341a0f1a8e1a20501a7df29fc9703f65::server:10997ae53b9103160ebf29f08cb944a5
INFO[2021-11-29T19:48:22.814688600+05:30] Starting k3s agent v1.21.5+k3s2 (724ef700)
WARN[2021-11-29T19:48:22.815245100+05:30] Error starting load balancer: listen tcp 127.0.0.1:6444: bind: address already in use
FATA[2021-11-29T19:48:22.815310100+05:30] listen tcp 127.0.0.1:6444: bind: address already in use

##### Not working

started following this - https://gist.github.com/ibuildthecloud/1b7d6940552ada6d37f54c71a89f7d00

downloaded latest k3s from here - https://github.com/k3s-io/k3s/releases/tag/v1.22.3+k3s1

Now I need to move/ or take that downloaded files in ubuntu terminal, so that i can run.

https://www.youtube.com/watch?v=3ymuCbe0TBg

##### there is no C or D drive in Linux, everything starts with slash /

So to get our windows files.

first do cd /

1: ls -ltr

it will list all.

2: cd mnt

going inside mnt

3: ls -ltr

it will list c,d, all drives

raajesh@LAPTOP-UN5K40HI:/mnt/c$ cd Users
raajesh@LAPTOP-UN5K40HI:/mnt/c/Users$ cd Admin
raajesh@LAPTOP-UN5K40HI:/mnt/c/Users/Admin$ cd Desktop
raajesh@LAPTOP-UN5K40HI:/mnt/c/Users/Admin/Desktop$ cd Raajesh
raajesh@LAPTOP-UN5K40HI:/mnt/c/Users/Admin/Desktop/Raajesh$ cd EMLO
raajesh@LAPTOP-UN5K40HI:/mnt/c/Users/Admin/Desktop/Raajesh/EMLO$ cd Session6K8sPyTorchKubeFlowHandsOn
raajesh@LAPTOP-UN5K40HI:/mnt/c/Users/Admin/Desktop/Raajesh/EMLO/Session6K8sPyTorchKubeFlowHandsOn$ ls -ltr
total 52008
drwxrwxrwx 1 raajesh raajesh     4096 Nov 28 17:58 Images
-rwxrwxrwx 1 raajesh raajesh    12494 Nov 29 16:00 README.md
-rwxrwxrwx 1 raajesh raajesh 53239808 Nov 29 19:23 k3s



raajesh@LAPTOP-UN5K40HI:/mnt/c/Users/Admin/Desktop/Raajesh/EMLO/Session6K8sPyTorchKubeFlowHandsOn$ sudo k3s server
INFO[2021-11-29T20:30:44.770476900+05:30] Starting k3s v1.21.5+k3s2 (724ef700)
INFO[2021-11-29T20:30:44.770860600+05:30] Cluster bootstrap already complete
FATA[2021-11-29T20:30:44.817081800+05:30] starting kubernetes: preparing server: init cluster datastore and https: listen tcp :6443: bind: address already in use

________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

raajesh@LAPTOP-UN5K40HI:~$ sudo k3s server & 

raajesh@LAPTOP-UN5K40HI:~$ sudo k3s kubectl get node
NAME              STATUS   ROLES                  AGE   VERSION
laptop-un5k40hi   Ready    control-plane,master   41h   v1.21.5+k3s2

raajesh@LAPTOP-UN5K40HI:~$ sudo cat /var/lib/rancher/k3s/server/node-token



raajesh@LAPTOP-UN5K40HI:~$ sudo k3s kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:6443
CoreDNS is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

raajesh@LAPTOP-UN5K40HI:~$ ip addr | grep eth0
4: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
​    inet 172.30.29.159/20 brd 172.30.31.255 scope global eth0
11: veth0b2e19a9@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default

raajesh@LAPTOP-UN5K40HI:~$ sudo k3s agent --server https://172.30.29.159:6443 --token K108211e20657f883ecc1c38126a427f166341a0f1a8e1a20501a7df29fc9703f65::server:10997ae53b9103160ebf29f08cb944a5
INFO[2021-12-01T11:46:49.988264900+05:30] Starting k3s agent v1.21.5+k3s2 (724ef700)
WARN[2021-12-01T11:46:49.988648900+05:30] Error starting load balancer: listen tcp 127.0.0.1:6444: bind: address already in use
FATA[2021-12-01T11:46:49.988700400+05:30] listen tcp 127.0.0.1:6444: bind: address already in use

raajesh@LAPTOP-UN5K40HI:~$ sudo k3s kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-r
esources?ref=$PIPELINE_VERSION"
namespace/kubeflow created
customresourcedefinition.apiextensions.k8s.io/clusterworkflowtemplates.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/cronworkflows.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/scheduledworkflows.kubeflow.org created
customresourcedefinition.apiextensions.k8s.io/viewers.kubeflow.org created
customresourcedefinition.apiextensions.k8s.io/workfloweventbindings.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/workflows.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/workflowtasksets.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/workflowtemplates.argoproj.io created
Warning: apiextensions.k8s.io/v1beta1 CustomResourceDefinition is deprecated in v1.16+, unavailable in v1.22+; use apiextensions.k8s.io/v1 CustomResourceDefinition
customresourcedefinition.apiextensions.k8s.io/applications.app.k8s.io created
serviceaccount/kubeflow-pipelines-cache-deployer-sa created
clusterrole.rbac.authorization.k8s.io/kubeflow-pipelines-cache-deployer-clusterrole created
clusterrolebinding.rbac.authorization.k8s.io/kubeflow-pipelines-cache-deployer-clusterrolebinding created

```shell
# env/platform-agnostic-pns hasn't been publically released, so you will install it from master
export PIPELINE_VERSION=1.7.1
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=$PIPELINE_VERSION"
kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/platform-agnostic-pns?ref=$PIPELINE_VERSION"
```

##### give sudo k3s before every command!

##### Above two worked.



##### Error and solution

1-After installing K3s for the first time on a Pi4, it ended with this error:

```sql
Unable to read /etc/rancher/k3s/k3s.yaml, please start server with --write-kubeconfig-mode to modify kube config permissions
```

Solution:

```perl
$ sudo chmod 644 /etce/rancher/k3s/k3s.yaml
```

**Steps To Reproduce:**

- Installed K3s:

```cpp
$ curl -sfL https://get.k3s.io | sh -
```

2-[[SOLVED\] Docker Failed to Start - Docker Desktop for Windows](https://forums.docker.com/t/solved-docker-failed-to-start-docker-desktop-for-windows/106976)

[suryac](https://forums.docker.com/u/suryac)

[Sep 18](https://forums.docker.com/t/solved-docker-failed-to-start-docker-desktop-for-windows/106976/7)



delete %appdata%\Docker\settings.json and let Docker to create a new one

