Repo we are referring for PyTorch Samples - https://github.com/kubeflow/pipelines/tree/master/samples/contrib/pytorch-samples 

Look at above link README, we are going to follow option 2.

##### Kubeflow local

In this repo, we are going to create pipelines using kfp and upload the created yaml to Kubeflow locally.

prerequisites - https://github.com/kubeflow/pipelines/blob/master/samples/contrib/pytorch-samples/prerequisites.md

https://github.com/kubeflow/pipelines/tree/master/sdk/python for option 2.

In future, we will be using AWS may be in session 7 or 8 or further.

##### Option 2. Compiling and Running by uploading to Kubeflow Pipelines

1-creating folder

mkdir tsai

cd tsai

then

git clone https://github.com/kubeflow/pipelines.git

after cloning, go inside pipelines, samples, contrib, pytorch-samples.

##### Type below to check files in WSL2 UBUNTU folder

##### \\wsl$\Ubuntu\home\raajesh

Put two slash in front..

##### YAML folder wont be there initially.

Use the following python files building the pipeline locally for Cifar 10 and Bert examples

Cifar 10 - [cifar10/pipeline.py](https://github.com/kubeflow/pipelines/blob/master/samples/contrib/pytorch-samples/cifar10/pipeline.py)

Bert - [bert/pipeline.py](https://github.com/kubeflow/pipelines/blob/master/samples/contrib/pytorch-samples/bert/pipeline.py)

looking at pipeline.py file inside Cifar 10 folder.

Assuming we have already created YAMLs for components, we load those here in this pipeline.py file.

Generate YAMLs - https://github.com/kubeflow/pipelines/blob/master/samples/contrib/pytorch-samples/utils/template-generation.md

raajesh@LAPTOP-UN5K40HI:~/tsai/pipelines/samples/contrib/pytorch-samples$ python3 utils/generate_templates.py cifar10/te
mplate_mapping.json
Processing copy_component.yaml
Processing preprocess_component.yaml
Processing minio_component.yaml
Processing ax_train_component.yaml
Processing ax_complete_trials_component.yaml
Processing ax_generate_trials_component.yaml
Processing tensorboard_component.yaml
Processing train_component.yaml
Processing deploy_component.yaml
Processing prediction_component.yaml

##### we need to use python3

Then installed pip

Try this command

```
sudo apt-get install python3-pip
```

**Note**: If you have a fresh install, you need to do this first before trying the above command.

```
sudo apt-get update
```

then

pip3 install -U pytorch-kfp-components

when I did this, system crashed, hope its installed.

then

pip3 install kfp

##### To generate final pipeline yaml

raajesh@LAPTOP-UN5K40HI:~/tsai/pipelines/samples/contrib/pytorch-samples$ python3 cifar10/pipeline.py

It created final yaml.