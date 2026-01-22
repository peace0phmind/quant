# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a quantitative stock analysis project that combines two Microsoft open-source frameworks:

- **RD-Agent**: AI R&D automation framework for autonomous data science
- **Qlib**: AI-oriented quantitative investment platform

The system uses multi-agent systems to automate quantitative trading research, including factor mining, model optimization, and strategy backtesting.

### Project Structure

```
/home/mind/quant/
├── data/              # Financial data storage (stocks, options, rates, earnings)
├── qlib/              # Qlib framework - quantitative investment platform
├── RD-Agent/          # RD-Agent framework - AI R&D automation
└── PROJECT_INFO.md    # Project documentation and git remote configuration
```

### Git Remote Configuration

- **origin**: Your fork (`git@github.com:peace0phmind/*`) - for commits
- **github**: Microsoft upstream (`git@github.com:microsoft/*`) - for syncing updates

Sync upstream: `git fetch github && git merge github/main`

---

## Development Commands

### Environment Setup

```bash
# Create conda environment (Qlib requires Python 3.8-3.11)
conda create -n rdagent python=3.10
conda activate rdagent

# Install RD-Agent for development
cd /home/mind/quant/RD-Agent
make dev

# Initialize Qlib environment (optional)
make init-qlib-env

# Health check (validates Docker and environment)
rdagent health_check
```

### Code Quality

```bash
cd /home/mind/quant/RD-Agent

# Linting (run these in order)
make mypy      # Type checking (rdagent/core only)
make ruff      # Python linting
make isort     # Import sorting
make black     # Code formatting
make toml-sort # TOML file sorting
make lint      # Run all linters

# Auto-fix
make auto-lint # Auto-fix format issues
```

### Testing

```bash
cd /home/mind/quant/RD-Agent

# Run all tests
make test

# Run offline tests only (no API calls)
make test-offline

# Run single test
pytest test/path/to/test.py::test_function

# Run with coverage
python -m pytest --cov=rdagent
```

---

## RD-Agent Architecture

RD-Agent implements an **evolving R&D framework** with two-agent system:

### Core Framework (`rdagent/core/`)

- **`conf.py`**: Configuration management using environment variables
- **`evolving_framework.py`**: Evolution strategies for iterative improvement
- **`proposal.py`**: Hypothesis generation for new experiments

### Key Components

1. **Research Agent (R)**: Proposes new ideas/hypotheses
2. **Development Agent (D)**: Implements and executes experiments
3. **Knowledge Management**: Stores and retrieves learnings from past experiments
4. **Feedback Loop**: Uses experiment results to guide evolution

### Workflow Loop

```
Hypothesis → Experiment Design → Code Implementation → Execution → Feedback → Evolution → Repeat
```

### Scenarios (`rdagent/scenarios/`)

- **`qlib/`**: Quantitative finance - factor and model co-optimization
- **`data_science/`**: General ML - Kaggle competitions, medical prediction
- **`general_model/`**: Paper/model extraction and implementation

---

## Main CLI Entry Points

All commands run from `/home/mind/quant`:

```bash
# Quantitative finance scenarios (Qlib-based)
rdagent fin_quant           # Full factor-model co-optimization
rdagent fin_factor          # Factor evolution only
rdagent fin_model           # Model evolution only
rdagent fin_factor_report --report-folder=<path>  # Extract factors from financial reports

# General data science
rdagent general_model <paper_url>       # Implement model from paper
rdagent data_science --competition <name>  # Kaggle/auto-ML scenario

# Utilities
rdagent health_check          # Validate environment
rdagent ui --port 19899 --log-dir log/  # View experiment logs (web UI)
rdagent ui --data_science     # View data science scenario logs
```

### Configuration

Configuration via `.env` file in project root:

```bash
# LLM configuration (LiteLLM backend)
CHAT_MODEL=gpt-4o
EMBEDDING_MODEL=text-embedding-3-small
OPENAI_API_BASE=<your_api_base>
OPENAI_API_KEY=<your_key>

# Or separate providers
CHAT_MODEL=deepseek/deepseek-chat
DEEPSEEK_API_KEY=<key>
EMBEDDING_MODEL=litellm_proxy/BAAI/bge-m3
LITELLM_PROXY_API_KEY=<key>
LITELLM_PROXY_API_BASE=https://api.siliconflow.cn/v1

# For reasoning models (o1, o3, etc.)
REASONING_THINK_RM=True
```

---

## Qlib Integration

Qlib (`/home/mind/quant/qlib/`) provides the quantitative investment infrastructure:

### Data Management

- Data stored in `~/.qlib/qlib_data/cn_data` by default
- Supports daily and 1-minute frequency data
- Automatic data updates via cron jobs

### Qlib Commands

```bash
cd /home/mind/quant/qlib

# Initialize data (one-time setup)
python -m qlib.cli.data qlib_data --target_dir ~/.qlib/qlib_data/cn_data --region cn

# Run workflow with config file
cd examples
qrun benchmarks/LightGBM/workflow_config_lightgbm_Alpha158.yaml

# Check data health
python scripts/check_data_health.py check_data --qlib_dir ~/.qlib/qlib_data/cn_data
```

### Qlib Components

- **Models**: LightGBM, LSTM, GRU, Transformer, HIST, TRA, TCN, ADARNN, etc. (in `examples/benchmarks/`)
- **Workflow**: Dataset preparation → Model training → Backtesting → Analysis
- **Analysis**: IC (Information Coefficient), cumulative returns, risk metrics

---

## Key Design Patterns

### Multi-Agent Coordination

- **Strategy Pattern**: Pluggable evolving strategies for different scenarios
- **Template Method**: Base workflow with customizable steps
- **Observer Pattern**: Feedback collection and propagation

### Configuration-Driven

- YAML configuration files for experiments (`conf_*.yaml`)
- Environment variables for LLM backends
- Scenario-specific settings in `rdagent/app/qlib_rd_loop/conf.py`

### Knowledge Retention

- Persistent knowledge bases across sessions
- Historical experiment results guide future proposals
- Factor and model co-evolution in quant scenarios

---

## Common Tasks

### Sync with Upstream

```bash
# For RD-Agent
cd /home/mind/quant/RD-Agent
git fetch github && git merge github/main

# For Qlib
cd /home/mind/quant/qlib
git fetch github && git merge github/main
```

### Run Quant Experiment

```bash
cd /home/mind/quant
rdagent fin_quant  # Runs factor-model co-optimization loop

# Monitor results
rdagent ui --log-dir log/
```

### Add New Factor/Model

1. Define in `rdagent/scenarios/qlib/`
2. Configuration in `rdagent/app/qlib_rd_loop/conf.py`
3. Agent will automatically generate implementation code
4. Results evaluated via backtesting in Qlib

### Debug Failed Experiment

1. Check logs in `log/` directory
2. Use `rdagent ui` for visual inspection
3. Review generated code in `git_ignore_folder/factor_implementation_source_data/`
4. Adjust configuration in `.env` or scenario config files

---

## Important Notes

- **Linux Only**: RD-Agent currently only supports Linux
- **Docker Required**: Most scenarios require Docker to be installed
- **Qlib Data**: Use community data source from `https://github.com/chenditc/investment_data/releases`
- **API Keys**: Configure LLM keys in `.env` before running experiments
- **Port Conflicts**: Default UI port is 19899; check with `rdagent health_check --no-check-env --no-check-docker`

---

## Documentation References

- RD-Agent: https://rdagent.readthedocs.io/
- Qlib: https://qlib.readthedocs.io/
- Live Demo: https://rdagent.azurewebsites.net/
- Technical Report: https://arxiv.org/abs/2505.14738
