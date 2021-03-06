# 待办清单

<!-- ## 内容新增

### Linux 性能优化实战笔记及拓展

### Bash Pitfalls 翻译完结

### github 仓库整理

### 调整访问方式 -->

## 内容规划

```mermaid
gantt
    dateFormat  YYYY-MM-DD
    title       整体更新进展规划
    excludes    weekends
    %% (`excludes` accepts specific dates in YYYY-MM-DD format, days of the week ("sunday") or "weekends", but not the word "weekdays".)

    section Linux 性能优化
    目录整理                             : done,          task_1, 2021-03-04, 3d
    CPU 篇                               : crit,         task_2, 2021-03-06, 10d
    内存篇                               :                task_3, after task_2, 10d
    磁盘 I/O 篇                          :                task_4, after task_3, 10d
    网络篇                               : crit,          task_5, after task_4, 15d
    综合篇                               : crit,          task_6, after task_5, 20d

    section Bash Pitfalls 翻译
    目录整理                             : done,           tmp_1, 2021-03-01, 2d
    Case 1~20                           :                 tmp_2, 2021-04-01, 6d
    Case 21~40                          :                 tmp_3, after tmp_2, 7d
    Case 41~60                          :                 tmp_4, after tmp_3, 10d

    section 高频必要工具
    明确清单                             : active,         tmp_9, 2021-03-06, 6d
    vim 总结                             : crit,           tmp_10, after task_1, 7d
    VS Code 整理                         : crit,           tmp_11, after task_2, 15d
    sed 整理                             :                 tmp_11, after task_4, 10d
    awk 整理                             :                 tmp_12, after tmp_11, 10d

    section 前置知识
    整理目录结构                          : active,         tmp_13, after tmp_12, 7d
    cheatsheet 整理                      :                 tmp_14, after tmp_13, 8d

    section 好用的工具
    计划清单                              : active,        tmp_15, after tmp_14, 5d
    资源监控                              :               tmp_16, after tmp_15, 5d
```



## 功能插件扩展

### 提示窗美化 —— 已完成 2021-03-04

> [!NOTE]
> An alert of type 'note' using global style 'callout'.

> [!TIP]
> An alert of type 'tip' using global style 'callout'.

> [!WARNING]
> An alert of type 'warning' using global style 'callout'.

> [!ATTENTION]
> An alert of type 'attention' using global style 'callout'.

### 阅读进度条 —— 已完成 2021-03-04

### Valine 评论

### 阅读时长 & 字数统计

### google 统计

### 运行时间统计

### 访问量统计

### 前后章节跳转 —— 已完成 2021-03-05

### 回到顶部

### 侧边栏目录折叠

### TOC 目录

### dark 模式 —— 未完成 2021-03-05
待调试

### 社交分享

### 图片点击放大 —— 已完成 2021-03-05

### mermaid 作图 —— 已完成 2021-03-05
原参考链接文档错了。
```mermaid
graph LR
    A[Square Rect] -- Link text --> B((Circle))
    A --> C(Round Rect)
    B --> D{Rhombus}
    C --> D
```

### 中英文添加空格 —— 已完成 2021-03-05
`测试：hello世界1234   哈哈  hehe`

测试：hello世界1234   哈哈  hehe

### font-awesome

### 代码复制

### 加载远端 markdown 文件

### 文内 Tab

### 文内 Slide

### 内嵌 GIF

### 内嵌 echarts

### 内嵌视频

### 内嵌 pdf

### 预加载提示

### Top banner 提示

### 左上角优化

### 右下角 live2d 展示

### emoji

### 文末打赏功能

### ······


## 参考

[awesome-docsify: Plugins](https://github.com/docsifyjs/awesome-docsify#plugins)

[mermaid manual](https://mermaid-js.github.io/mermaid/#/)

[markdown-cheatsheet](https://github.com/tchapi/markdown-cheatsheet)