---
{"dg-publish":true,"permalink":"/Boxes/差异表达分析（DEG）/","tags":["input"]}
---

其分析结果为**找到两组间显著差异的基因**。
缺点是：可能会漏掉一些**单独变化不大、但处于调控网络关键位置**的基因

# 筛选逻辑
LogFC **>0**：基因在该组中**上调**
adj.P.Val（矫正后的P值）
**过滤条件：LogFC>1 and adj.P.Val<0.05**

## FC：倍数变化，可调整
若认为FC>2，认为差异表达，则log$2$FC>1
若认为FC>1.5,                         则            >0.58
![Attachments/Pasted image 20250929150628.png](/img/user/Boxes/Attachments/Pasted%20image%2020250929150628.png)
# R语言实现
## R包介绍
![Attachments/Pasted image 20250927131401.png|450](/img/user/Boxes/Attachments/Pasted%20image%2020250927131401.png)

# 绘图表现
箱式图
火山图
？差异基因热图