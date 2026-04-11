## 1. output_dir 是什么
训练结果保存目录，里面会包含模型、日志、配置等内容。

output_dir = 本地实验目录

 --output_dir [Path]   Set `dir` to where you would like to save all of the run outputs. If you run another training session with the same value for `dir` its contents will be overwritten unless you set `resume`to true. (default: None)  
checkpoints/<step>/pretrained_model = 某一步保存的模型  
train_config.json = 恢复训练要用的配置  
policy.path = 评估时通常指向某个 checkpoint 下的 pretrained_model

## 2. checkpoint 是什么
训练过程中的阶段性存档，用于恢复训练或对比不同阶段效果。

 第一层checkpoint ：是训练过程中的关键保存节点，

 官方训练命令里支持 `--save_checkpoint=true` 和 `--save_freq=...`，说明 checkpoint 是按步数定期保存的。  

第二层`**pretrained_model**`**预训练模型**  
这是后面评估或继续使用 policy 时最常直接指向的路径。官方评估命令示例就是把 `--policy.path` 指到 `.../checkpoints/<step>/pretrained_model`。

第三层**恢复训练配置**  
如果你不是从头训，而是想接着某个 checkpoint 继续跑，就会用到 `train_config.json`，官方恢复训练示例已经这么做了。

## 3. 最终 policy 是什么
训练完成后真正用于推理、评估和部署的策略模型。

## 4. 日志是什么
记录训练过程中的 loss、step 等变化。  
学习率、评估指标

## 5. 关键参数
- batch size：每次取多少样本训练，和显存、训练速度有关  
- training steps：训练总步数  
- learning rate：参数更新步长  
- eval frequency：隔多少步做一次评估  
- save frequency：隔多少步保存一次 checkpoint  
  
这些参数通常由训练配置传入，也可以通过命令行覆盖部分参数。

以上这些参数都是从TrainPipelineConfig传进去的

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/65987704/1775921928356-bbf0cdb8-c550-44e8-9f5d-d866917e33d1.png)



## 6. 我的理解
训练命令负责启动实验，output_dir 负责保存实验结果，checkpoint 负责中途恢复，最终 policy 负责后续推理。

在cmd中执行lerobot --help   
发现训练命令的全部参数中包含几大块，分别是  
训练基础设置、性能设置、日志与保存、高级训练算法、数据格式

## 训练完成后优先看：
1. 是否成功生成输出目录 根据--output_dir路径查看  
2. 是否有 checkpoint，断点训练  
3. 是否有最终 policy，最终的policy要进行模型推理  
4. 是否有日志记录，log日志查看训练论文，损失函数，准确率等指标  
5. loss 是否正常变化，观察loss确定模型好坏



## Issue List
+ output_dir 和 policy.repo_id 的区别 
    - `output_dir`：**本地**训练结果保存目录 
    - `policy.repo_id`：更偏向**模型发布/保存到 Hub 的标识**
+  wandb 和本地日志谁是必须的：
    - 本地日志是必须的，训练过程中的基础记录，通常更直接关系到本地排查和结果查看
    - wandb是可选的实验记录与可视化工具
    -  本地日志更像默认训练记录，wandb 是可选的实验可视化与追踪工具。  

### train_step代替了传统的epoch：
**LeRobot 的 ACT 训练是以 **`**steps**`** 为主的 step-based training；epoch 仍然可能出现在日志里，但更像统计意义上的训练进度，而不是主控制变量。**



## 总结：
**训练不是只有命令，训练还有产物、保存、恢复、评估和后续复用。**

