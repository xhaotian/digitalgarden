---
{"dg-publish":true,"permalink":"/Boxes/统计之光--COX回归分析/","tags":["fleeting","brief_note"]}
---

# 单因素COX回归分析
用于预测**单一因素对预后影响的大小**。
若所得**HR及其CI>1，且P<0.05**，则认为该因素为**危险因素**，即促进结果发生。
原数据：
![Attachments/Pasted image 20250928141001.png](/img/user/Boxes/Attachments/Pasted%20image%2020250928141001.png)

而后必须对各有预后价值的基因绘制**生存曲线**，以均值或**中位数**绘制高表达或地表达组，做生存率-时间比较
![Attachments/Pasted image 20250927135331.png|400](/img/user/Boxes/Attachments/Pasted%20image%2020250927135331.png)

# 多因素COX回归分析
## ROC曲线
**ROC曲线**越靠上：预测模型越可靠
靠近对角线：效果一般        AUC=0.5
低于对角线：无法使用
![Attachments/Pasted image 20250927140254.png|425](/img/user/Boxes/Attachments/Pasted%20image%2020250927140254.png)
此图可见三年预测效果最好

## 生存分析验证
内部验证（同一数据集）
外部验证（额外数据集）

![Attachments/Pasted image 20250927140831.png](/img/user/Boxes/Attachments/Pasted%20image%2020250927140831.png)
此图示外部验证可见模型的一年生存率预测能力最佳

## 独立影响验证
分为其它组，看看结果有无差异
以下验证可见：模型预测结果不受淋巴浸润和神经浸润的影响（其实不能说其是一个独立的预后预测因子）

![Attachments/Pasted image 20250927141303.png](/img/user/Boxes/Attachments/Pasted%20image%2020250927141303.png)