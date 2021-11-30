This repo explains how to install and work with Kubeflow locally.

If we created pipeline JSONs and YAMLs using kfp and want to deploy the same locally, then we can use Kubeflow.

We can also take those JSONs, YAMLs and create main.py files and take it to GCP too and deploy the pipeline in Vertex AI - https://github.com/emlopsinfy/Simple-KubeFlow-Pipeline-and-deploying-to-GCP

This session repo uses AWS in the future, may be in Session 7 or 8 or further.

##### Theory...

##### What is Kubeflow ?

Kubeflow (originated at Google) is an end-to-end ML platform for Kubernetes, it provides components for each stage in the ML lifecycle, from exploration through to training and deployment. Operators can choose what is best for their users, there is no requirement to deploy every component.  Kubeflow is the ML toolkit for Kubernetes. The following diagram shows Kubeflow as a platform for arranging the components of your ML system on top of Kubernetes:

##### Architecture

![Create Jobs](https://github.com/emlopsinfy/Session6K8sPyTorchKubeFlowHandsOn/blob/2328ee0b52b867860405e63ae02e36804aa6dbf2/Images/Kubeflow%20architecture.PNG)

##### Components

![Create Jobs](https://github.com/emlopsinfy/Session6K8sPyTorchKubeFlowHandsOn/blob/2328ee0b52b867860405e63ae02e36804aa6dbf2/Images/Kubeflow%20components.PNG)

**Kubeflow Pipelines:**

Kubeflow pipelines can be defined through a YAML specification, Python SDK, or by annotating existing Python code or Jupyter Notebooks. The YAML file is a declaration of the container images participating in the pipeline, the entry point for each container, and the location to persist the artifacts.

![Create Jobs](https://github.com/emlopsinfy/Session6K8sPyTorchKubeFlowHandsOn/blob/fe78e260b43d1709611cc9e3d2ea77d562a63b9f/Images/Component%20example.PNG)

The Python SDK and API provide programmatic access to the pipeline runtime. They make it possible to extend an existing Python application to leverage the workflow.

##### Component performs one step in the pipeline, containerized implementation of an ML task.

##### Graph shows relationship between components.

**Component Types**


Kubeflow contains two types of components, one for rapid development and one for re-usability.

**Lightweight Component:** Used for fast development in a notebook environment. Fast and easy cause there is no need to build container images.

**Reusable Containerized Component:** Can be reused by loading it into a Kubeflow pipeline. It is a containerized component.
Require more implementation time.

**Reusable Component**

A component itself is simple and consists of just a few parts:

The component logic

A component specification as yaml.

A Dockerfile is required to build the container.

A readme to explain the component and its inputs and outputs.

A helper script to build the component and push it to a Docker repository.

**Component Specification**

Written in YAML format (component.yaml):

- - **Metadata** describe the component itself, like name and description
  - The **interface** defines the input and the output of the component.
  - **Implementation** specifies how the component should be executed.


**Handling Input**

A component requires usually some kind of input, like a path to our training data or the name of our model. It can consume multiple inputs.

Define inputs in the component.yaml

Define the input as arguments for our container.

Parse arguments in the component logic.

**Handling Output**

The output is required to pass data between components. It is important to know that each component in a pipeline **executes independently**.

- - Components run in different processes and cannot share data.
  - The process for passing small data differs from large data.
  - For small data:
    - Values can be passed directly as output.
  - **For large data:** 
    - Large data has to be **serialized** to files to be passed between components.
    - **Upload** the data to our storage system.
    - And **pass a reference** to this file to the next component.
    - The next **component** in the pipeline will take this reference and **download** the serialized data.

##### Dockerize Component
Each component is a container image that requires a Dockerfile in order to build an image.

**Use the Pipeline**

Load the Component
Everyone with access to the Docker repository and the component.yaml can use the component in Pipelines.

![img](https://miro.medium.com/max/700/1*YN1P-2pkbka1mOWILWCSGA.png)

The component can then be loaded based on the component.yaml.

##### operation = kfp.components.load_component_from_url(‘xxx’)

**Create the Pipeline**

The **dsl** decorator is provided via the pipeline SDK and is used to define and interact with pipelines. **dsl.pipeline** defines a decorator for Python functions that returns a pipeline. 

![Create Jobs](https://github.com/emlopsinfy/Session6K8sPyTorchKubeFlowHandsOn/blob/91c95fc18119cacbfb3a4b9df280347210ee3ac5/Images/create%20pipeling.PNG)



**Compile the Pipeline**

To compile the pipeline we use the compiler.Compile() function which is again part of the pipeline SDK. The compiler generates a yaml definition which is used by Kubernetes to create the execution resources.

![Create Jobs](https://github.com/emlopsinfy/Session6K8sPyTorchKubeFlowHandsOn/blob/cb36d569dfef70d0d8414c3bd74b31035b03f7f5/Images/pipeline%20compiler.PNG)

**Create an Experiment**

Pipelines are always part of an experiment and can be created with the Kubeflow Pipeline Client kfp.client().

![Create Jobs](https://github.com/emlopsinfy/Session6K8sPyTorchKubeFlowHandsOn/blob/d763ed1d4b738b04685c37433756b6959b117c23/Images/create%20experiment.PNG)

**Run the Pipeline**

To run a pipeline we use the experiment id and the compiled pipeline created in the previous steps. client.run_pipeline runs the pipelines and provides a direct link to the Kubeflow experiment.

![Create Jobs](https://github.com/emlopsinfy/Session6K8sPyTorchKubeFlowHandsOn/blob/d763ed1d4b738b04685c37433756b6959b117c23/Images/run%20pipeline.PNG)

[**ArgoWorkflow** (Links to an external site.)](https://github.com/argoproj/argo-workflows)

##### Kubeflow internally uses Argo to manage pipeline.

Kubeflow pipelines heavily rely on container images, Argo Workflow for orchestration.

**What is Argo Workflows?**

Argo Workflows is an open-source container-native workflow engine for orchestrating parallel jobs on Kubernetes. Argo Workflows is implemented as a Kubernetes CRD (Custom Resource Definition).

- - - Define workflows where each step in the workflow is a container.
    - Model multi-step workflows as a sequence of tasks or capture the dependencies between tasks using a directed acyclic graph (DAG).
    - Easily run compute-intensive jobs for machine learning or data processing in a fraction of the time using Argo Workflows on Kubernetes.
    - Argo Workflows is the most popular workflow execution engine for Kubernetes.
    - It can run 1000s of workflows a day, each with 1000s of concurrent tasks.
    - Users say it is lighter-weight, faster, more powerful, and easier to use
    - Designed from the ground up for containers without the overhead and limitations of legacy VM and server-based environments.
    - Cloud agnostic and can run on any Kubernetes cluster.
    - Let's check its [features (Links to an external site.)](https://github.com/argoproj/argo-workflows).

[**MinIO** (Links to an external site.)](https://min.io/?utm_content=inline-mention)

Kubeflow pipelines heavily rely on MinIO for data persistence.

- - - MinIO offers high-performance, S3 compatible object storage.
    - Native to Kubernetes, MinIO is the only object storage suite available on every public cloud, every Kubernetes distribution, the private cloud and the edge.

**Notebook Server**

Kubeflow comes with an integrated Jupyter Notebook platform in the form of Kubeflow Notebook Servers. Each Notebook Server is based on a container image that comes with the libraries, frameworks, and tools needed by a data scientist team. 

![Create Jobs](https://github.com/emlopsinfy/Session6K8sPyTorchKubeFlowHandsOn/blob/1a3aae4c9d6fae9b2b5fa0c4d3a7564540240bfd/Images/notebook%20server.PNG)

_______________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

##### 

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



##### How to create YAMLs

https://github.com/kubeflow/pipelines/tree/master/samples/contrib/pytorch-samples