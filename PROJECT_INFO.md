# Quant 项目说明

## 项目概述
量化股票分析项目，基于微软开源框架进行开发。

## 项目结构
```
/home/mind/quant/
├── data/          # 分析数据存储目录
│   ├── earnings/  # 财报数据
│   ├── options/   # 期权数据
│   ├── rates/     # 利率数据
│   └── stocks/    # 股票数据
├── qlib/          # 微软 QLIB 框架 (AI量化投资平台)
├── RD-Agent/      # 微软 RD-Agent (AI研发代理)
├── CLAUDE.md      # Claude Code 项目指令
├── PROJECT_INFO.md # 项目说明文档
└── .gitignore     # Git 忽略配置
```

## 环境配置

### Conda 环境
- **环境名称**: `quant`
- **Python 版本**: 3.11
- **CUDA 版本**: 12.8 (系统路径: `/usr/local/cuda-12.8`)
- **Conda 路径**: `/home/mind/conda`

### 激活环境
```bash
conda activate quant
```

### 已安装框架
| 框架 | 版本 | 安装模式 |
|------|------|----------|
| RD-Agent | 0.8.1.dev10 | Development (`make dev`) |
| Qlib | 0.9.8.dev22 | Development (`make dev`) |

### 验证安装
```bash
# 验证 RD-Agent
rdagent --help
rdagent health_check

# 验证 Qlib
python -c "import qlib; print(qlib.__version__)"
qrun --help
```

## Git 配置

### 全局用户信息
- **用户名**: `peace0phmind`
- **邮箱**: `peace0phmind@gmail.com`

### Git Remote 配置

### 主项目 (quant)
- **origin**: `git@github.com:peace0phmind/quant.git` (项目协调仓库)

### QLIB
- **origin**: `git@github.com:peace0phmind/qlib.git` (个人 fork，用于提交修改)
- **github**: `git@github.com:microsoft/qlib.git` (上游仓库，用于同步更新)

### RD-Agent
- **origin**: `git@github.com:peace0phmind/RD-Agent.git` (个人 fork，用于提交修改)
- **github**: `git@github.com:microsoft/RD-Agent.git` (上游仓库，用于同步更新)

## 工作流程

### 定期同步上游更新
```bash
cd /home/mind/quant/qlib
git fetch github
git merge github/main

cd /home/mind/quant/RD-Agent
git fetch github
git merge github/main
```

### 提交修改到个人仓库
```bash
git add .
git commit -m "描述修改内容"
git push origin main
```

## 说明
- 所有本地修改仅提交到 `origin` (个人 fork)
- 定期从 `github` (微软上游) 同步最新代码
- 不直接向上游仓库提交 PR 或修改
