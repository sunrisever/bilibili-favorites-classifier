---
name: bilibili-favorites
description: "B站收藏夹智能分类与同步。触发词：B站收藏、收藏夹整理、bilibili收藏分类、收藏夹分组、视频收藏整理"
---

# B站收藏夹分类助手

AI 辅助整理 B站收藏夹，完成采集、规则生成、分类审核与收藏夹同步。

## 触发关键词

B站收藏、收藏夹整理、bilibili收藏分类、收藏夹分组、视频收藏整理、整理收藏夹

## 适用场景

- 收藏的视频已经很多，想按主题重组
- 希望先自动分类，再人工复核低置信度项
- 想把本地分类结果同步回 B站收藏夹结构

## 运行前提

- 在 `data/config.json` 中配置 B站 Cookie
- 在 `data/config.json` 中配置用于分析/审核的 LLM API
- 运行环境默认使用 Anaconda `base`
- 安装依赖：`bilibili-api-python>=17.4.1`、`anthropic>=0.18.0`、`httpx>=0.24.0`

## 数据与隐私

- 账号 Cookie、API Key、原始收藏数据都应留在本地
- 建议把敏感配置放在 `local/` 或未纳入版本控制的 `data/config.json`
- 发布前按 [RELEASE_CHECKLIST.md](RELEASE_CHECKLIST.md) 检查隐私与示例数据

## 标准工作流

### 1. 采集收藏数据

```bash
python fetch.py all
python fetch.py resume
python fetch.py stats
```

输出：

- `data/收藏视频数据.json`
- `data/fetch_checkpoint.json`

### 2. 生成分类规则

```bash
python analyze.py
python analyze.py summary
```

输出：

- `data/classify_rules.json`

### 3. 导入 UP 主分类映射

```bash
python import_up_map.py
python import_up_map.py "path/to/bilibili-follow-project"
```

输出：

- `data/up_classify_map.json`

### 4. 三阶段分类

```bash
python classify.py
python classify.py algo
python classify.py ai
python classify.py review
```

输出：

- `data/分类结果.json`

### 5. 生成可读文档

```bash
python generate_info.py
```

输出：

- `data/视频信息汇总.txt`
- `data/分类结果.md`

### 6. 预览并同步到 B站

```bash
python sync.py --dry-run
python sync.py
```

## 辅助脚本

### 增量处理新收藏

```bash
python add_new.py
python add_new.py --days 30
```

### 恢复丢失视频

```bash
python recover.py --dry-run
python recover.py
```

## 风险提醒

- `sync.py` 会重建非默认收藏夹，必须先 `--dry-run`
- AI 审核会消耗模型额度
- Cookie 过期或 B站风控会导致同步失败
- 这个项目与 `bilibili-follow` 配合使用效果更好：一个整理关注，一个整理收藏
