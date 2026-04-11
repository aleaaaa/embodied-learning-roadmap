##  任务 1：抄下官方 ACT 训练命令  
ACT works seamlessly with the standard LeRobot training pipeline. Here’s a complete example for training ACT on your dataset:

```bash
lerobot-train \
  --dataset.repo_id=${HF_USER}/your_dataset \  
  --policy.type=act \  
  --output_dir=outputs/train/act_your_dataset \  
  --job_name=act_your_dataset \ 
  --policy.device=cuda \ 
  --wandb.enable=true \  
  --policy.repo_id=${HF_USER}/act_policy  
```

**拿一个数据集，指定 policy=ACT，把训练结果保存到输出目录，并在 GPU 上训练。** ACT 文档还说明，ACT 的默认超参数对大多数任务都够用，训练通常可以在单张 GPU 上几小时内完成，batch size 可以先从 8 起步。  



##  任务 2：逐个参数翻译  
你今天至少把这几个参数看懂：

+ `--dataset.repo_id`：训练数据集在哪 
+ `--policy.type=act`：这次训练策略用的 ACT policy 
+ `--output_dir`：训练输出保存路径
+ `--job_name`：这次任务名字 
+ `--policy.device=cuda`：用 GPU 跑 
+ `--wandb.enable=true`：是否启用实验记录 
+ `--policy.repo_id`：训练好的 policy 未来要保存到哪里 

你可以把它理解成：

**数据在哪 + 用什么模型 + 在哪训练 + 结果存哪。**

****

## ** 任务 3：看评估命令  **
Once training is complete, you can evaluate your ACT policy using the `lerobot-record` command with your trained policy. This will run inference and record evaluation episodes:

```plain
lerobot-record \
  --robot.type=so100_follower \
  --robot.port=/dev/ttyACM0 \
  --robot.id=my_robot \
  --robot.cameras="{ front: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30}}" \
  --display_data=true \
  --dataset.repo_id=${HF_USER}/eval_act_your_dataset \
  --dataset.num_episodes=10 \
  --dataset.single_task="Your task description" \
  --dataset.streaming_encoding=true \
  --dataset.encoder_threads=2 \
  # --dataset.vcodec=auto \
  --policy.path=${HF_USER}/act_policy
```

机器臂的型号，摄像头端口，机器人id、相机参数

display_data？，数据位置、dataset.num_episodes？

任务描述、dataset.streaming_encoding？

dataset.encoder_threads？

policy.path？  
评估命令更看重硬件的参数、数据的encode和episodes。 评估命令更偏硬件和执行配置。  

训练时在更细ACT算法的参数，推理时在用训练的参数实现任务

解答：

`display_data`：通常就是是否显示采集/运行过程中的数据可视化。  
`dataset.num_episodes`：评估或记录多少条 episode。  
`dataset.streaming_encoding`：边记录边编码数据，避免全部原始数据后处理。  
`dataset.encoder_threads`：编码线程数，不是“编码阈值”。  
`policy.path`：评估时加载的训练好 policy 的位置。  



##  任务 4：把命令和训练主循环对应起来  
训练命令行在确定ACT算法训练程序的参数  
命令行与train其实一一对应  
`--dataset.repo_id`的数据路径对应dataloader中的数据集路径

`--policy.type=act` 这次训练选用的策略模型类型是 ACT  

`--policy.device=cuda` → 模型在哪跑 

`--output_dir` → 结果往哪存 

 训练过程中隐含的就是：dataloader、forward、loss、backward、optimizer.step



## 产出：
今天主要学习了ACT的训练指令，和Day3训练流程相对应，训练指令其实就是指导ACT算法，数据集在哪、用CUDA训练还是CPU，训练策略是什么，训练完的结果存储位置在那

## 问题
+ `wandb.enable=true` 具体会记录什么 
+  评估时为什么还是用 `lerobot-record`
+  训练命令里 batch size 和 step 数在哪改

