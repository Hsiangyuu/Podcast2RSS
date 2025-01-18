# Podcast RSS Generator
这个项目用于生成指定播客的RSS Feed，支持音频转写功能

## 功能特点
- 自动获取播客更新
- 通过通义API调用进行音频转写
- 生成标准RSS Feed
- 静态文件服务

## 项目运行流程

1. **播客数据获取** (`podcast.py`)
   - 通过小宇宙API获取订阅的播客列表
   - 通过config文件获取指定播客的最新所有单集信息
   - 将播客列表subscribe_podcasts.json和单集列表pid.json保存到本地数据目录

2. **音频转写处理** (`podcast_transcription.py`)
   - 剧集收集（EpisodeCollector）
     1. 扫描本地剧集目录
     2. 过滤条件检查：
        - 验证剧集数据完整性
        - 检查音频时长（跳过超过5小时的音频）
        - 确认未转写状态
     3. 生成待处理剧集列表
   
   - 转写处理（TranscriptionProcessor）
     1. 任务准备
        - 解析音频URL获取任务内容
        - 构建有效任务列表
     2. 批量转写
        - 提交转写任务（默认每批10个）
        - 定期检查任务状态（30秒间隔）
        - 记录任务完成情况（完成/失败/运行中）
        - 超时控制（1小时）
     3. 任务清理
        - 清理未完成任务
        - 清理失败任务
     4. 结果处理
        - 获取转写文本
        - 保存转写结果
   
   - 数据存储
     - 保存完整的转写文本

3. **RSS生成** (`rss.py`)
   - 整合播客信息、单集数据和转写内容
   - 生成标准的RSS Feed格式

数据流：小宇宙API → 本地数据存储 → 通义听悟转写 → RSS Feed生成，实现了从播客订阅到RSS生成的完整自动化流程。