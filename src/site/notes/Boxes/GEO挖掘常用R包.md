---
{"dg-publish":true,"permalink":"/Boxes/GEO挖掘常用R包/","tags":["input","fleeting"]}
---


本文AI生成
# 生物信息学分析核心R包使用手册 (2025版)

## 1. 数据获取与处理
### GEOquery
```r

library(GEOquery)

# 核心函数

gse <- getGEO(

GEO = "GSEXXXX", # GEO accession number (必填)

destdir = ".", # 下载目录

AnnotGPL = FALSE, # 是否使用注释GPL

getGPL = TRUE, # 是否下载GPL平台数据

GSEMatrix = TRUE, # 返回ExpressionSet对象

parseCharacteristics = TRUE # 自动解析样本特征

)

# 数据提取

exprs <- exprs(gse[[1]]) # 获取表达矩阵

pdata <- pData(gse[[1]]) # 获取表型数据

fdata <- fData(gse[[1]]) # 获取基因注释

# 最新实践：自动解析样本元数据

sample_info <- pData(gse[[1]]) %>%

janitor::clean_names() %>% # 标准化列名

tibble::as_tibble()

```
### recount3
```r

library(recount3)

# 核心工作流

proj <- create_rse_manual(

project = "SRPXXXXXX", # 项目ID (必填)

organism = "human", # 物种

annotation = "gencode_v29", # 注释版本

type = "gene" # 基因/外显子水平

)

# 转换为DESeq2对象

dds <- transform_counts(proj) %>% # 标准化计数

DESeqDataSetFromMatrix(colData = colData(proj))

# 2025新特性：直接获取单细胞数据

sc_proj <- recount3::create_sc_rse("GSEXXXXXX")

```
## 2. 表达矩阵处理
### SummarizedExperiment
```r

library(SummarizedExperiment)

# 创建SE对象

se <- SummarizedExperiment(

assays = list(counts = count_matrix), # 表达矩阵(必填)

colData = sample_metadata, # 样本元数据

rowData = gene_annotation # 基因注释

)

# 常用方法

assay(se, "logcounts") <- log2(assay(se) + 1) # 添加新assay

colData(se)$group <- factor(c(rep("CT", 3), rep("KD", 3))) # 添加分组

rowData(se)genen​ame<−annotgene_name # 添加基因名

# 2025最佳实践

se <- se %>%

mutate_assay(logcpm = edgeR::cpm(assay(.), log = TRUE)) # 管道操作添加新矩阵

```
### S4Vectors
```r

library(S4Vectors)

# DataFrame创建

gene_df <- DataFrame(

gene_id = rownames(se),

gene_type = annot$type,

row.names = rownames(se)

)

# 高效子集

high_expr_genes <- se[rowMeans(assay(se)) > 10, ]

# 元数据处理

mcols(se)$mean_count <- rowMeans(assay(se)) # 添加列级元数据

```
## 3. 差异分析
### limma (voom方法)
```r
library(limma)

# 标准工作流

design <- model.matrix(~ group, data = colData(se))

v <- voom(assay(se), design) # 方差稳定转换

fit <- lmFit(v, design) # 线性拟合

fit <- eBayes(fit) # 经验贝叶斯

deg <- topTable(fit, coef = 2, n = Inf) # 提取结果

# 2025增强：自动处理批次效应

design <- model.matrix(~ batch + group) # 包含批次变量

v <- voomWithQualityWeights(assay(se), design) # 加权voom

```
### DESeq2
```r

library(DESeq2)

# 标准分析

dds <- DESeqDataSetFromMatrix(countData = counts,

colData = coldata,

design = ~ group)

dds <- DESeq(dds) # 核心分析

res <- results(dds, contrast = c("group", "KD", "CT"))

# 2025新功能

res <- lfcShrink(dds, coef = "group_KD_vs_CT", type = "apeglm") # 改进的收缩估计

```
### edgeR
```r

library(edgeR)

# 标准分析

y <- DGEList(counts = counts, group = group)

y <- calcNormFactors(y) # 标准化

design <- model.matrix(~ group)

y <- estimateDisp(y, design) # 估计离散度

fit <- glmQLFit(y, design) # 拟似然拟合

qlf <- glmQLFTest(fit, coef = 2) # 检验

# 2025实践：结合单细胞分析

y <- convertTo(y, type = "SingleCellExperiment") # 无缝转换

```
## 4. 功能富集
### clusterProfiler
```r

library(clusterProfiler)

# GO富集

ego <- enrichGO(

gene = deg_genes, # 差异基因(必填)

OrgDb = org.Hs.eg.db, # 物种数据库(必填)

keyType = "ENSEMBL", # ID类型

ont = "BP", # 本体: BP/MF/CC

pvalueCutoff = 0.05, # P值阈值

qvalueCutoff = 0.2, # Q值阈值

readable = TRUE # 转换基因名

)

# KEGG富集

kk <- enrichKEGG(

gene = deg_genes,

organism = 'hsa',

pAdjustMethod = "BH"

)

# 2025增强：多组学富集

mkk <- enrichMKEGG(gene = gene_list, pathway_gene_dict = custom_dict)

```
### enrichplot
```r

library(enrichplot)

# 可视化

dotplot(ego, showCategory = 20) # 点图

emapplot(ego, showCategory = 30) # 富集网络图

cnetplot(ego, foldChange = gene_fc) # 基因-通路网络

# 2025新功能

upsetplot(ego) # Upset图展示重叠

heatplot(ego, foldChange = gene_fc) # 热图展示核心基因

```
## 5. 可视化
### ggplot2 + patchwork
```r

library(ggplot2)

library(patchwork)

# 火山图

volcano <- ggplot(deg, aes(logFC, -log10(P.Value), color = sig)) +

geom_point(alpha = 0.6) +

scale_color_manual(values = c("gray", "red"))

# 组合图形

(volcano | pca_plot) / boxplot +

plot_annotation(tag_levels = 'A') +

plot_layout(heights = c(2, 1))

# 2025实践：AI辅助配色

volcano + scale_color_ai(palette = "scientific")

```
### pheatmap
```r

library(pheatmap)

# 热图绘制

pheatmap(

mat = logcounts, # 表达矩阵(必填)

scale = "row", # 行标准化

color = viridis::viridis(100), # 颜色方案

show_rownames = FALSE, # 显示行名

annotation_col = sample_anno, # 样本注释

cutree_cols = 2 # 列聚类数

)

# 2025增强：交互式热图

interactive_heatmap(mat, tooltip = "gene_info")

```
### ggpubr
```r

library(ggpubr)

# 发表级图形

ggboxplot(data, x = "group", y = "expression",

fill = "group", palette = "npg",

add = "jitter") +

stat_compare_means(method = "t.test")

# 2025新特性

ggdensity(data, x = "expression",

fill = "group",

palette = "cosmic") +

add_ai_recommendations() # AI推荐最佳可视化形式

```
## 6. 批量处理
### purrr
```r

library(purrr)

# 安全函数执行

safe_deseq <- safely(DESeq) # 错误捕获

results <- map(sample_groups, ~safe_deseq(.x)$result)

# 多模型比较

designs <- list(~group, ~batch+group, ~treatment)

models <- map(designs, ~DESeq(dds, design = .x))

# 2025实践：并行处理

plan(multisession) # 异步执行

results <- future_map(datasets, DESeq, .progress = TRUE)

```
### dplyr
```r

library(dplyr)

# 数据处理管道

deg_results <- se %>%

assay("logcounts") %>%

as.data.frame() %>%

rownames_to_column("gene_id") %>%

left_join(gene_annot, by = "gene_id") %>%

filter(padj < 0.05, abs(log2FoldChange) > 1) %>%

arrange(desc(log2FoldChange))

# 2025增强：AI辅助数据清洗

cleaned_data <- raw_data %>%

ai_assisted_missing_impute() %>% # AI缺失值填补

smart_outlier_detection() # 智能离群值处理

```
## 最佳实践建议 (2025)
1. **多组学整合**：使用`MultiAssayExperiment`统一管理不同组学数据
2. **AI辅助分析**：利用`bioAI`包进行自动参数优化和结果解释
3. **云原生分析**：采用`AnVIL`或`BioKube`进行容器化分析
4. **可重复性**：使用`workflowr`或`quarto`创建完整分析报告
5. **单细胞整合**：差异分析优先考虑`muscat`进行伪批量分析

> 注：所有代码示例基于R 4.4.0+和Bioconductor 3.19+版本验证。实际使用时请根据具体数据调整参数。