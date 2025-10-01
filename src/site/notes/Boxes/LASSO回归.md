---
{"tags":["input","fleeting"],"project":"GEO数据挖掘","modified":["2025-10-02","2025-09-26","2025-09-25"],"dg-publish":true,"permalink":"/Boxes/LASSO回归/","dgPassFrontmatter":true}
---

>[! note]  LASSO回归总结
>LASSO回归是一种变量降维手段，其作用是从线性模型中筛选出对结果影响大的变量们。
>可控制回归的参数主要是λ，λ不同得到的最终变量数量也不同。
>图1主要是展示λ与变量数量的关系
>图2主要是展示λ与似然偏差的关系，常选择λ\[min-se\]
>
>（K折交叉验证的K也对结果有影响，但我还不太懂其意义）
# 原理
![Attachments/Pasted image 20250926134824.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926134824.png)
![Attachments/Pasted image 20250926134830.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926134830.png)
![Attachments/Pasted image 20250926134907.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926134907.png)
![Attachments/Pasted image 20250926140507.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926140507.png)![Attachments/Pasted image 20250926140647.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926140647.png)![Attachments/Pasted image 20250926140648.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926140648.png)![Attachments/Pasted image 20250926140756.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926140756.png)![Attachments/Pasted image 20250926212415.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926212415.png)![Attachments/Pasted image 20250926205907.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926205907.png)
![Attachments/Pasted image 20250926210143.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926210143.png)![Attachments/Pasted image 20250926212356.png](/img/user/Boxes/Attachments/Pasted%20image%2020250926212356.png)

# 应用
肿瘤：预后
非肿瘤：鉴别诊断

模型（prognostic nodel）/ signature/ riskscore

# 非肿瘤应用代码
# 肿瘤应用代码
# 建模/SCORE
![Attachments/Pasted image 20250925085732.png](/img/user/Boxes/Attachments/Pasted%20image%2020250925085732.png)

从网盘中或B站再找相关视频及代码