# 使用FasterRCNN预置算法基于海量数据训练人车检测模型

在自动驾驶方案实现的过程中，物体检测是其中一个重要的技术。本案例基于ModelArts AI Gallery中官方发布的物体检测算法`FasterRCNN`，训练一个人车检测模型，并部署成在线服务，生成一个人车检测AI应用。

ModelArts的AI Gallery有丰富的算法，使用这些算法，无需自己开发训练代码和推理代码，只要准备并标注好数据，就可以轻松快速训练并部署模型。

## 准备工作

参考[此文档](https://github.com/huaweicloud/ModelArts-Lab/blob/master/docs/ModelArts准备工作/准备工作简易版.md)，完成ModelArts准备工作,包括注册华为云账号、ModelArts全局配置和OBS相关操作。

## 准备数据

### 下载训练数据集

本案例采用自动驾驶场景的数据集，数据集中有两种物体，人和车。

本案例的数据集已经发布在AI Gallery，我们从华为云AI Gallery订阅数据集至ModelArts，然后就可以在ModelArts中使用了。点击[此链接](https://marketplace.huaweicloud.com/markets/aihub/datasets/detail/?content_id=3e587ec7-71bc-4949-8dcf-4c9b08673963)进入下载详情页，下载详情页示例如下：

![car](./img/数据下载.png)

下载方式：选择ModelArts数据集

目标区域：华北-北京四

目标位置：选择一个OBS路径，作为数据集的存储位置。

名称：自定义。

填写好参数后，点击![food](./img/下一步.png)按钮，然后点击![food](./img/确定.png)按钮。等待数据集状态变为推送成功，即可在[ModelArts数据集列表](https://console.huaweicloud.com/modelarts/?region=cn-north-4#/dataset)中查看到下载的数据集。

### 发布数据集

点击进入[ModelArts数据集列表](https://console.huaweicloud.com/modelarts/?region=cn-north-4#/dataset)，找到刚订阅的数据集，点击“发布”按钮，填写训练集比例为0.8，发布数据集。数据集发布之后，才可在训练中使用。

![create_dataset](./img/发布数据集.png)

![create_dataset](./img/发布数据集2.png)

等待数据集发布成功。

## 订阅算法

本实验中，我们从AI Gallery订阅ModelArts官方发布的物体检测算法`FasterRCNN`来训练模型。

点击进入AI Gallery[FasterRCNN算法主页](https://console.huaweicloud.com/modelarts/?region=cn-north-4#/aiMarket/aiMarketModelDetail/overview?modelId=198573e9-b2a9-46ef-aca9-51cc651b6364&type=algo)，点击页面右上方的![food](./img/订阅.png)按钮。然后点击页面下方的![food](./img/继续订阅.PNG)按钮，再点击<img src="./img/前往控制台.PNG" alt="food" style="zoom:75%;" />按钮，选择华北-北京四区域，最后点击![food](./img/确定.png)按钮进入算法管理页面，可以看到刚刚订阅的算法。

点击“同步”按钮，同步算法，可以点击![food](./img/刷新.png)按钮，刷新状态。当状态变成就绪时，表示同步成功。

![food](./img/同步算法.png)

## 模型训练

我们使用创建的人车检测数据集，提交一个人车检测训练作业，训练会生成一个人车检测模型。

### 创建训练作业

回到[ModelArts训练管理页面](https://console.huaweicloud.com/modelarts/?region=cn-north-4#/trainingJobs)，在【训练管理】选择训练作业，点击【创建】，如下图所示：

![create_obs](./img/创建训练作业.png)

在创建训练作业页面中选择算法：

![选择算法](./img/选择算法.PNG)

选择算法，(算法列表是按订阅时间显示的,找到名称为`物体检测-FasterRCNN_ResNet50`的算法,选中它)

![算法管理](./img/算法管理.PNG)

按照如下提示，填写创建训练作业的参数。

![train](./img/训练作业参数1.png)

计费模式：按需计费

名称：自定义

算法来源：算法管理

算法名称：`物体检测-FasterRCNN_ResNet50`

数据来源：数据集

选择数据集和版本：选择刚刚发布的人车数据集及其版本

![train](./img/训练作业参数2.png)

训练输出：选择OBS路径`/lftiny/car_and_person_detection/output/`（此OBS路径如果不存在，可以使用OBS客户端创建）。训练输出位置用来保存训练生成的模型。

调优参数：用于设置算法中的超参。算法会加载默认参数，但是可以更改和添加参数。设置`learning_rate_strategy=20:0.001`，表示训练20轮，学习率固定为0.001。其他调优参数保持默认。

![train](./img/训练作业参数3.png)

作业日志路径：选择OBS路径`/lftiny/car_and_person_detection/log/`（此OBS路径如果不存在，可以使用OBS客户端创建）。

![train](./img/训练作业参数4.png)

资源池：公共资源池。

规格：` [限时免费]GPU: 1*NVIDIA-V100-pcie-32gb(32GB) | CPU: 8 核 64GB`，如图所示。

计算节点个数：选择1，表示我们运行一个单机训练任务。

所有字段填写好之后，确认参数无误，点击“下一步”按钮，然后点击“提交”按钮，开始训练。

使用V100 GPU资源，训练时长预计30分钟左右。

### 查看训练结果

训练作业完成后，可以查看训练作业的运行结果。

在训练作业页面，点击作业名称，进入配置信息页面。可以查看到训练作业的详情。

![food](./img/训练详情.png)

切换到“日志”页签，查看训练作业的训练日志，还可以下载日志到本地查看。

训练日志中会打印一些训练的精度和训练速度等信息。

训练生成的模型会放到训练输出位置OBS路径下，可以直接下载到本地使用。

## 模型部署

### 导入模型

点击“创建模型”按钮，创建模型。

![food](./img/创建模型.png)

按照如下提示，填写导入模型的字段。

![food](./img/导入模型字段1.png)

名称：自定义

版本：0.0.1

![food](./img/导入模型字段2.png)

元模型来源：从训练中选择

选择训练作业及版本：刚刚的训练作业及版本，会自动加载

部署类型：默认

推理代码：自动加载

其他保持默认。

点击“立即创建”按钮，开始导入模型，等待模型导入成功。

### 部署上线

等待模型状态为正常，然后点击部署下拉框中的“在线服务”，如下图所示：

![food](./img/部署上线.png)

按照如下指导填写参数：

![food](./img/部署上线参数1.png)

计费模式：按需计费

名称：自定义

是否自动停止：开启，一小时后。会在1小时后自动停止该在线服务。

![food](./img/部署上线参数2.png)

资源池：公共资源池。如果您购买了专属资源池，也可以选择专属资源池部署。

模型来源：我的模型

模型：选择刚刚导入美食人车检测的模型和版本，会自动加载。

计算节点规格：选择[限时免费]`CPU：1 核 4 GiB`。

计算节点个数：1。如果想要更高的并发数，可以增加计算节点个数，会以多实例的方式部署。

填写好所有参数，点击“下一步”按钮，然后点击“提交”按钮，最后点击查看服务详情。状态栏会显示部署进度，大概3分钟左右会部署完成。

### 在线服务测试

在线服务的本质是RESTful API，可以通过HTTP请求访问，在本案例中，我们直接在网页上访问在线服务。

等待在线服务的状态变成运行中。

先点[此链接](https://modelarts-labs-bj4.obs.cn-north-4.myhuaweicloud.com:443/end2end/car_and_person_detection/car_and_person_test.zip)下载测试集，解压，再切换到预测标签，点击上传按钮，进行测试

预测结果会出现在右边的输出框：

![food](./img/预测结果.PNG)



预测结果中的detection_classes字段，表示识别的分类（人与车）

预测结果中的detection_boxes字段，表示识别框的四个角坐标

预测结果中的detection_scores字段，表示图片为每种类别的置信度

## 关闭在线服务

为了避免持续扣费，案例完成后，需要关闭在线服务，点击“停止”按钮即可：

![food](./img/停止服务.png)

当需要使用该在线服务的时候，可以重新启动该在线服务。

### 确认关闭所有计费项

点击[此链接](https://console.huaweicloud.com/modelarts/?region=cn-north-4#/manage/dashboard)，进入ModelArts总览页面，如果所有计费中的数字都是0，表示所有计费项都关闭了。

![train](./img/总览.PNG)

**案例完成。**

