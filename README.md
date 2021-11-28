This repo explains how to install and work with Kubeflow locally.

##### Theory...

##### What is Kubeflow ?

Kubeflow (originated at Google) is an end-to-end Machine Learning (ML) platform for Kubernetes, it provides components for each stage in the ML lifecycle, from exploration through to training and deployment. Operators can choose what is best for their users, there is no requirement to deploy every component.  Kubeflow is the ML toolkit for Kubernetes. The following diagram shows Kubeflow as a platform for arranging the components of your ML system on top of Kubernetes:

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

 

**Dockerize Component**
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















