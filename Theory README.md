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

