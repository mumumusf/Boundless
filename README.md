# Boundless 证明节点部署指南

[![GitHub](https://img.shields.io/badge/GitHub-mumumusf%2FBoundless-blue)](https://github.com/mumumusf/Boundless)

这是一个简化的 Boundless 证明节点部署脚本，帮助您快速搭建和运行 Boundless 网络上的证明节点。

## 📋 系统要求

### 最低配置要求

| 配置 | 需求 |
|------|------|
| **CPU** | 16 cores |
| **RAM** | 32GB |
| **GPUs** | 至少一个 NVIDIA GPU ≥ 8GB |
| **磁盘/存储** | 200 GB 固态存储，首选 NVME/SSD |
| **操作系统** | Ubuntu 20.04/22.04 |

### 推荐的 GPU 型号

- **RTX 4090** - 24GB 显存，性能优秀
- **RTX 5090** - 最新旗舰，性能卓越  
- **Tesla L4** - 专业级 GPU，稳定性高

### 性能建议

- **入门级**: RTX 4070/4080 (12-16GB 显存)
- **中端级**: RTX 4090 (24GB 显存)
- **高端级**: RTX 5090 或 Tesla L4 (24GB+ 显存)
- **专业级**: 多卡配置 (2-4 张 GPU)

### 网络要求

- **带宽**: 至少 100Mbps 稳定连接
- **延迟**: 低延迟连接以获得更好的证明效率
- **稳定性**: 24/7 不间断运行

## 🚀 快速开始

### 1. 更新系统包

```bash
# 更新包列表并升级系统
apt update && apt upgrade -y

# 安装 wget（如果未安装）
apt install wget -y
```

### 2. 下载并运行安装脚本

#### 方式一：直接下载安装（推荐）

```bash
# 下载安装脚本
wget https://raw.githubusercontent.com/mumumusf/Boundless/main/Boundless.sh -O Boundless.sh

# 设置执行权限
chmod +x Boundless.sh

# 运行安装脚本
bash Boundless.sh
```

#### 方式二：克隆仓库安装

```bash
# 克隆仓库
git clone https://github.com/mumumusf/Boundless.git
cd ~/Boundless

# 设置执行权限
chmod +x Boundless.sh

# 运行安装脚本
bash Boundless.sh
```

## 🔧 配置说明

### 网络选择

脚本支持以下网络：

1. **Base 主网** - 生产环境，需要真实资金
2. **Base Sepolia 测试网** - 测试环境，使用测试代币
3. **以太坊 Sepolia 测试网** - 测试环境，使用测试代币

### RPC 配置

推荐使用以下 RPC 提供商：

- **BlockPi** - Base 网络免费
- **Alchemy** - 设置 `lookback_block=<120`
- **Chainstack** - 设置 `lookback_blocks=0`
- **您自己的节点**

### 质押要求

- 需要 USDC 质押才能参与证明
- 最低质押金额请查看官方文档
- 质押通过管理脚本进行

### Broker 配置参数详解

安装过程中，您需要配置以下关键参数。这些参数直接影响您的节点性能和收益：

#### 📊 配置参数说明

| 参数 | 说明 | 默认值 | 优化建议 |
|------|------|--------|----------|
| **peak_prove_khz** | 系统订单处理速度 | 100 | 根据实际性能设置 |
| **mcycle_price** | 每百万周期价格 | 0.0000005 | 接近最低限制 |
| **lockin_priority_gas** | 锁定交易额外gas | 0 | 4000-40000 Gwei |
| **max_mcycle_limit** | 最大接受周期数 | 8000 | 根据硬件能力 |
| **max_concurrent_proofs** | 最大并行证明数 | 2 | 建议保持2 |
| **min_deadline** | 接受订单的最小剩余时间 | 300 | 150-200秒 |

#### 🎯 实战配置示例

**RTX 4090 配置示例**：
```toml
peak_prove_khz = 751
mcycle_price = "0.000000000000000111"
lockin_priority_gas = 4000
max_mcycle_limit = 500000
max_concurrent_proofs = 2
min_deadline = 200
```

#### ⚡ 性能计算公式

```
peak_prove_khz = (总周期 / 订单总时间) × 1000
```

**实际案例**：
- 7.3M周期 / 992秒 = 7.3 kHz
- 98M周期 / 300秒 = 326 kHz

#### 🔧 参数优化建议

##### 1. **peak_prove_khz** - 关键参数
- **作用**: 系统订单处理速度，值越高获得订单越多
- **风险**: 设置过高会导致超时被slash
- **建议**: 
  - 新手：使用默认值100
  - 有经验：根据实际性能保守设置
  - 测试网：可以大胆实验

##### 2. **mcycle_price** - 价格策略
- **作用**: 每百万周期的价格报价
- **策略**: 接近系统最低限制以获得竞争力
- **风险**: 价格过低可能利润很少
- **建议**: 在测试网上可以设置很低的价格

##### 3. **lockin_priority_gas** - 竞标优势
- **作用**: 在竞标中击败其他证明者
- **推荐值**: 4000 Gwei
- **风险**: 设置过高（如40000）可能出错
- **优势**: 提高赢得竞标的机会

##### 4. **max_mcycle_limit** - 能力上限
- **作用**: 接受的最大周期数（百万）
- **建议**: 根据硬件能力设置
  - 入门级：8000-50000
  - 中端级：50000-200000
  - 高端级：200000-500000

##### 5. **max_concurrent_proofs** - 并行处理
- **作用**: 同时处理的最大证明数
- **推荐值**: 2（安全值）
- **风险**: 3-4会接更多订单但可能无法完成

##### 6. **min_deadline** - 接受订单的最小剩余时间
- **作用**: 接受订单的最小剩余时间（秒）
- **含义**: "至少还有多少秒剩余时间的订单我才接受？"
- **策略**: 
  - 低值 → 连风险高的订单也接受（更激进）
  - 高值 → 只接受安全时间长的订单（更保守，但可能错过订单）
- **推荐值**: 150-200秒
- **建议**: 
  - 新手：使用默认值300秒
  - 有经验：150-200秒
  - 测试网：可以设置更低值实验

#### ⚠️ 重要注意事项

1. **性能波动**: 实际性能可能在7.3-326 kHz之间波动
2. **RPC影响**: RPC性能会影响证明速度
3. **保守策略**: 建议设置保守的peak_prove_khz值
4. **测试网优势**: 在测试网上可以安全实验配置
5. **超时风险**: 如果不能在规定时间内完成证明，会被slash

#### 🎮 配置策略

**新手策略**：
- 使用脚本默认配置
- 在测试网上实验
- 逐步调整参数

**进阶策略**：
- 参考实战配置
- 根据硬件调整
- 监控性能波动

**专业策略**：
- 多GPU配置
- 付费RPC
- 精确性能调优

## 📊 管理节点

安装完成后，使用管理脚本：

```bash
cd ~/boundless
chmod +x prover.sh   # 如果还没执行过
bash prover.sh
```

### 管理功能

- **服务管理**: 启动/停止 broker 和 bento 服务
- **日志查看**: 实时查看运行日志
- **健康检查**: 系统状态监控
- **配置管理**: 更改网络、私钥等
- **质押管理**: 存入和查看质押余额
- **性能测试**: GPU 基准测试
- **GPU 监控**: 实时 GPU 状态

## 🔍 故障排除

### 常见问题

1. **GPU 驱动问题**
   ```bash
   nvidia-smi  # 检查 GPU 状态
   ```

2. **Docker 问题**
   ```bash
   systemctl status docker  # 检查 Docker 服务
   docker ps  # 查看运行中的容器
   ```

3. **网络连接问题**
   ```bash
   curl -X POST "YOUR_RPC_URL" \
     -H "Content-Type: application/json" \
     -d '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
   ```

### 日志位置

- **完整日志**: `/var/log/boundless_prover_setup.log`
- **错误日志**: `/var/log/boundless_prover_error.log`
- **容器日志**: `docker compose logs [service_name]`

## 📈 性能优化

### GPU 配置

根据显存大小自动设置 `SEGMENT_SIZE`：

| 显存 | SEGMENT_SIZE |
|------|-------------|
| ≥40GB | 22 |
| ≥20GB | 21 |
| ≥16GB | 20 |
| ≥12GB | 19 |
| ≥8GB | 18 |
| <8GB | 17 |

### 系统优化

```bash
# 监控 GPU
nvtop

# 监控系统
htop

# 查看磁盘使用
df -h
```

## 🔒 安全注意事项

1. **私钥安全**
   - 私钥存储在 `~/.env.*` 文件中
   - 文件权限设置为 600（仅所有者读写）
   - 不要分享或泄露私钥

2. **网络安全**
   - 使用安全的 RPC 端点
   - 定期更新系统和依赖
   - 监控异常活动

3. **备份重要数据**
   - 定期备份配置文件
   - 保存质押证明记录

## 📚 相关链接

- [Boundless 官方文档](https://docs.beboundless.xyz/)
- [Base 网络](https://base.org/)
- [RISC Zero](https://risczero.com/)
- [GitHub 仓库](https://github.com/mumumusf/Boundless)

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建功能分支
3. 提交更改
4. 推送到分支
5. 创建 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## ⚠️ 免责声明

- 本脚本仅供学习和研究使用
- 在生产环境中使用前请充分测试
- 作者不对使用本脚本造成的任何损失负责
- 请遵守当地法律法规

---

**注意**: 运行证明节点需要技术知识和风险意识。请确保您了解相关风险后再进行操作。 