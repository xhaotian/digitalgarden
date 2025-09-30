---
{"date":"2025-03-22","tags":["input"],"project":"MR_rep","modified":"2025-09-24,2025-05-02","dg-publish":true,"permalink":"/_root/R环境配置/","dgPassFrontmatter":true}
---

# R语言及一般包的配置

> 参考视频：【孟德尔随机化R包安装的详细教程】 
> [https://www.bilibili.com/video/BV18C4y1u727/?share_source=copy_web&vd_source=697b390cdf87cfb6325a30bfe37c925c](https://www.bilibili.com/video/BV18C4y1u727/?share_source=copy_web&vd_source=697b390cdf87cfb6325a30bfe37c925c)

1. 用国内镜像网站下载R、Rstudio、Rtools，用自带**默认地址**

2. 将tools→global option→Packages→primary CRAN repository 选国内镜像源

3. 包安装

![image-20250322225258736](https://typora-notepic.oss-cn-shanghai.aliyuncs.com/image-20250322225258736.png)

# 特别的包的下载
## 可用的形式

```r
install.packages("")
BiocManager::install("VariantAnnotation")
devtools::install_github("mrcieu/gwasglue",force = TRUE)
remotes::install_github("mrcieu/gwasglue", force = TRUE)
```

上面的形式会下载稳定版，
后面两个可以更新、开发版，可能不稳定。

## ★★★超时问题解决
1. 改超时默认时间
```r
options(timeout = 9999999999999999999999)
# 超时错误为最常见的错误
devtools::install_github("mrcieu/gwasglue")
```

2. **轻蜂加速器**★★★ 
一个月￥38，可以尝试，可以登陆合法境外网站，不涉及翻墙