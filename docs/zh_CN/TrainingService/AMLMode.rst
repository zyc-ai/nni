**在 Azure Machine Learning 上运行 Experiment**
===================================================

NNI 支持在 `AML <https://azure.microsoft.com/zh-cn/services/machine-learning/>`__ 上运行 Experiment，称为 aml 模式。

设置环境
-----------------

步骤 1. 参考 `指南 <../Tutorial/QuickStart.rst>`__ 安装 NNI。   

步骤 2. 通过此 `链接 <https://azure.microsoft.com/zh-cn/free/services/machine-learning/>`__ 创建 Azure 账户/订阅。 如果已有 Azure 账户/订阅，跳过此步骤。

步骤 3. 在机器上安装 Azure CLI，参照 `此 <https://docs.microsoft.com/zh-cn/cli/azure/install-azure-cli?view=azure-cli-latest>`__ 安装指南。

步骤 4. 从 CLI 验证您的 Azure 订阅。 要进行交互式身份验证，请打开命令行或终端并使用以下命令：

.. code-block:: bash

   az login

步骤 5. 使用 Web 浏览器登录 Azure 帐户，并创建机器学习资源。 需要选择资源组并指定工作空间的名称。 之后下载 ``config.json``，该文件将会在后面用到。

.. image:: ../../img/aml_workspace.png
   :target: ../../img/aml_workspace.png
   :alt: 


步骤 6. 创建 AML 集群作为计算集群。

.. image:: ../../img/aml_cluster.png
   :target: ../../img/aml_cluster.png
   :alt: 


步骤 7. 打开命令行并安装 AML 环境。

.. code-block:: bash

   python3 -m pip install azureml
   python3 -m pip install azureml-sdk

运行实验
-----------------

以 ``examples/trials/mnist-pytorch`` 为例。 NNI 的 YAML 配置文件如下：

.. code-block:: yaml

   experimentName: example_mnist
   searchSpaceFile: search_space.json
   trialConcurrency: 1
   maxExperimentDuration: 1h
   maxTrialNumber: 10
   useAnnotation: false
   tuner:
     name: TPE
     classArgs:
       optimize_mode: maximize
   trialCommand: python3 mnist.py
   trialCodeDirectory: .
   trialGpuNumber: 1
   trainingService:
     platform: aml
     subscription_id: ${replace_to_your_subscriptionId}
     resource_group: ${replace_to_your_resourceGroup}
     workspace_name: ${replace_to_your_workspaceName}
     compute_target: ${replace_to_your_computeTarget}
     docker_image: msranni/nni
     max_trial_number_per_gpu: 1
     use_active_gpu: False
   sharedStorage:
     storage_type: AzureBlob
     storage_account_name: ${replace_to_your_storage_account_name}
     container_name: ${replace_to_your_container_name}
     storage_account_key: ${replace_to_your_storage_account_key}
     local_mount_point: /mnt/data/
     remote_mount_point: /tmp/data
     local_mounted: usermount

注意：如果用 aml 模式运行，需要将 YAML 文件中的 ``trainingService`` 设置为 ``platform: aml`` 。

与 `本机模式 <LocalMode.rst>`__ 的 Trial 配置相比，aml 模式下的键值还有：


* docker_image

  * 必填。 作业中使用的 Docker 映像名称。 NNI 支持 ``msranni/nni`` 的映像来跑 jobs。

.. Note:: 映像是基于 cuda 环境来打包的，可能并不适用于 aml 模式 CPU 集群。

trainingService:


* subscription_id

  * 必填，Azure 订阅的 Id

* resource_group

  * 必填，Azure 订阅的资源组

* workspace_name

  * 必填，Azure 订阅的工作空间

* compute_target

  * 必填，要在 AML 工作区中使用的计算机集群名称。 `参考文档 <https://docs.microsoft.com/zh-cn/azure/machine-learning/concept-compute-target>`__ 了解步骤 6。

* max_trial_number_per_gpu

  * 可选，默认值为 1。 用于指定 GPU 设备上的最大并发 Trial 的数量。

* use_active_gpu

  * 可选，默认为 false。 用于指定 GPU 上存在其他进程时是否使用此 GPU。 默认情况下，NNI 仅在 GPU 中没有其他活动进程时才使用 GPU。

amlConfig 需要的信息可以从步骤 5 下载的 ``config.json`` 找到。

运行以下命令来启动示例示例 Experiment：

.. code-block:: bash

   git clone -b ${NNI_VERSION} https://github.com/microsoft/nni
   cd nni/examples/trials/mnist-pytorch

   # modify config_aml.yml ...

   nnictl create --config config_aml.yml

将 ``${NNI_VERSION}`` 替换为发布的版本或分支名称，例如：``v2.0``。
