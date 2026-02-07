---
{"dg-publish":true,"permalink":"/Boxes/TCGA样本名编码信息/","tags":["input","fleeting"]}
---

![v2-3505fccf54c1c038ba082410dc0b477d_1440w](/img/user/Boxes/Attachments/v2-3505fccf54c1c038ba082410dc0b477d_1440w.jpg)

TCGA的样本命名 
Sample ：其中编号**01~09表示肿瘤**，**10~19表示正常对照**。（区分正常和癌症样本的凭证）
CodeDefinition 
01 Primary Soild Tumor( 原发性实体肿瘤 ） 
02 Recurrent Soild Tumor( 复发性实体肿瘤 
03 Primary Blood Derived Cancer -Peripheral Blood(原发性 血源性-外周血 
04 Recurrent Blood Derived Cancer -Bone Blood(复发性血源性-骨髓） 
05 Additional -New Primary
06Metastatic( 转移肿瘤 
07 Additional Metastatic 
08 Human Tumor Original Cells( 肿瘤原始细胞 
09 Primary Blood Derived Cancer -Bone Marrow(原发性血源性-骨髓） 
10 Blood Derived Nomal
11Soild Tissue Normal 

01**A**：癌症组织 
01**B**：福尔马林浸泡样本 
02A：复发组织 
06A：转移组织 


## 例子
**Analyte：分析的分子类型**

看一个例子：

TCGA-A6-6650-01A-11R-1774-07

**TCGA**

Project所有TCGA样本名均以这个开头

**A6**

Tissue source site组织来源编码，

如A6就表示来源于Christiana Healthcare中心的结肠癌组织

**6650**

Participant参与者编号

**01**

**Sample编号01~09为癌症组织，10~19表示正常对照**

**A**

Vial在一系列患者组织中的顺序，绝大多数样本该位置编码都是A;

很少数的是B，表示[福尔马林固定石蜡包埋组织](https://zhida.zhihu.com/search?content_id=213643624&content_type=Article&match_order=1&q=%E7%A6%8F%E5%B0%94%E9%A9%AC%E6%9E%97%E5%9B%BA%E5%AE%9A%E7%9F%B3%E8%9C%A1%E5%8C%85%E5%9F%8B%E7%BB%84%E7%BB%87&zhida_source=entity)，已被证明用于测序分析的效果不佳，所以不建议使用01B的样本数据

**11**

PortionPortion 同属于一个患者组织的不同部分的顺序编号，同一组织会分割为100-120mg的部分，分别使用；

**R**

Analyte分析的分子类型，对应RNA

**1774**

Plate在一系列96孔板中的顺序，值大表示制板越晚

**07**

Center测序或鉴定中心编码

  

对RNA数据来说，Analyte为R的优先级最好，其次是R和T，而对于DNA层面的分析来说，D的优先级最高。

如果Analyte相同，那就选择Portion和/或Plate值更大的。
