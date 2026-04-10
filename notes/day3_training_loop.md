##  1. 训练主循环是什么  
其实就是将数据分batch，也就是分批次进行处理，根据GPU和CPU来；将数据送入前向传播中，根据损失函数计算真实值与预测值的偏差；在反向传播计算梯度，不断的优化参数，找到损失最小值。

##  2. 五个关键模块  
1.数据加载：dataload，分batch

2.前向传播：forward，model（x）前向计算

3.损失函数：loss ， 计算偏差

3.反向传播：backward， 反向传播

4.参数更新： step  ，更新参数

## 3. 我的理解  
我手写的pytorch的循环伪代码

```python
model  #确定模型
data #确定输入数据

#for循环遍历数据
for batch in dataloader:
    x, y = batch
    pred = model(x)
    loss = loss_fn(pred, y)

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
    

```

## 4. 在 ACT 里分别对应什么
1. dataloader  
在 ACT 中，dataloader 提供一批训练样本 batch。  
其中通常包含多路相机图像、当前机器人状态，以及真实动作序列。
2. forward  
forward 对应 ACT policy 的前向计算。  
模型根据输入观测进行编码和解码，输出动作预测结果，并计算训练 loss。
3. loss  
ACT 的 loss 主要反映预测动作序列和真实动作序列之间的误差。
4. backward  
loss.backward() 计算 loss 对模型各个可学习参数的梯度，告诉模型每个参数应该往哪个方向调整。
5. optimizer.step  
optimizer.step() 根据梯度更新 ACT policy 中的可学习参数，例如网络中的权重和偏置。



```python
# Run training loop
    step = 0
    done = False
    while not done:
        for batch in dataloader:
            batch = preprocessor(batch)
            loss, _ = policy.forward(batch)
            loss.backward()
            optimizer.step()
            optimizer.zero_grad()

            if step % log_freq == 0:
                print(f"step: {step} loss: {loss.item():.3f}")
            step += 1
            if step >= training_steps:
                done = True
                break
```

## 5.疑问的地方：
 “输入”和“监督标签”在ACT中是什么  

#### 训练时
+  输入：图像、当前关节状态、`z` 相关条件 
+  标签：真实未来动作序列 
+  目的：让模型预测的动作块尽量接近人类演示动作块 

#### 推理时
+  输入：图像、当前关节状态 
+  没有标签 
+  输出：模型自己生成的未来 `k` 步动作块

