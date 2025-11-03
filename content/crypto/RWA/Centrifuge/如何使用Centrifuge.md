---
{"publish":true,"created":"2025-10-27T14:04:20.601+08:00","modified":"2025-11-03T20:57:16.894+08:00","cssclasses":""}
---


Centrifuge 是一个专注于现实世界资产（RWA）代币化的 DeFi 协议，帮助用户将线下资产（如发票、房地产、信贷）转化为链上代币，并通过流动性池连接到 DeFi 生态。以下是基于官方文档和教程的完整使用指南，适用于初学者。整个过程可在测试网（Amber）上实践，避免真实资金风险。预计上手时间：10-20 小时。前提：具备区块链基础知识（如以太坊智能合约）。

#### 先决条件（Prerequisites）

- **硬件/软件**：一台联网电脑、浏览器（如 Chrome）、支持以太坊的钱包（如 MetaMask 或 Talisman）。
- **知识要求**：了解 DeFi 基础（如流动性池、代币标准 ERC-20/721）。
- **账户准备**：创建钱包账户，安全存储私钥。从 [Amber 水龙头](https://docs.centrifuge.io/networks/amber#faucet) 获取测试代币（如 CFG 测试版）。
- **开发工具**（可选，用于高级用户）：Node.js (v18+)、Yarn、Rust（用于 SDK）、Git。

#### 设置环境（Setup）

1. **安装钱包**：下载 MetaMask 或 Polkadot.js 扩展，创建/导入账户。
2. **连接 Centrifuge**：访问 [Centrifuge 官网](https://centrifuge.io/) 或 [仪表板](https://app.centrifuge.io/)，点击“连接钱包”。选择 Amber 测试网（网络 ID: 12345）。
3. **安装 SDK（开发者模式）**：克隆 GitHub 仓库：
    
    text
    
    ```
    git clone https://github.com/centrifuge/centrifuge.git
    cd centrifuge
    yarn install
    ```
    
    配置 .env 文件（添加 RPC 如 wss://amber.centrifuge.io 和私钥）。测试连接：yarn dev。
4. **注册账户**：在平台上创建 Centrifuge 账户（API 密钥从官网获取），用于访问 RWA Launchpad。

#### 代币化 RWA（Tokenizing Real-World Assets）

Centrifuge 使用 RWA Launchpad 简化代币化，将资产转化为 NFT 或 ERC-20 代币。

1. **识别资产**：选择线下资产（如未付发票、房地产权益），评估价值并准备法律文件（合规证明、KYC）。
2. **准备元数据**：上传资产细节到 IPFS（如法律文档的哈希）。
3. **铸造代币**：通过 Launchpad 或 SDK：
    - UI 方式：在 [app.centrifuge.io](https://app.centrifuge.io/) 选择“Tokenize Asset”，输入资产名称、描述、价值，生成智能合约。
    - 代码方式（SDK）：
        
        javascript
        
        ```
        import { mintRWA } from '@centrifuge/sdk';
        const rwaToken = await mintRWA({
          asset: { name: 'InvoiceRWA', description: 'Tokenized Invoice', value: '10000 USD' },
          metadata: { legalDoc: 'ipfs://QmExampleHash' },
          rpc: 'wss://amber.centrifuge.io'
        });
        ```
        确认交易在浏览器（如 Centrifuge Explorer）中。
4. **验证**：使用 Chainlink 等预言机验证资产真实性，确保完全抵押化。

#### 创建和管理池子（Creating and Managing Pools）

池子是 RWA 的流动性容器，支持借贷和投资。

1. **创建池子**：在仪表板选择模板（如结构化信贷或房地产），设置参数（抵押率 150%、利率 5%、到期日）。
    - UI：点击“Create Pool”，输入代币地址和投资者权限。
    - 代码：
        
        javascript
        
        ```
        import { createPool } from '@centrifuge/sdk';
        const pool = await createPool({ 
          template: 'structured-credit', 
          params: { collateral: '1000000', interestRate: '5%', maturity: '365 days' },
          rpc: 'wss://amber.centrifuge.io'
        });
        ```
        
2. **注入资产**：将代币化 RWA 转移到池子合约地址。
3. **管理池子**：监控仪表板（TVL、投资者参与），处理争议，分配收益（基于性能自动分发）。
4. **退出/结算**：到期后，通过合约取款本金和利息。

#### 集成与高级使用（Integrations）

1. **连接 DeFi**：桥接到 Aave、MakerDAO 或 Uniswap，提供流动性（e.g., 用 RWA 作为抵押借贷）。
2. **连接 TradFi**：与传统机构合作，通过 API 桥接线下资产（如发票融资）。
3. **社区工具**：加入 DAO 治理，使用 Tinlake dApp（旧版池子管理）。
4. **测试与优化**：在 Amber 测试网模拟交易；高级用户可参与黑客松或贡献 GitHub。

**注意**：始终确保合规（KYC/AML），主网使用真实资金前咨询法律专家。资源：[官方文档](https://docs.centrifuge.io/)、[YouTube 教程](https://www.youtube.com/watch?v=jqtofeJI3AU)。

### 完整理解 Centrifuge 在 DeFi 和 TradFi 上的技术实现路径

Centrifuge 的核心是桥接 TradFi（传统金融）和 DeFi（去中心化金融），通过 RWA 代币化实现资产流动性。截至 2025 年，它已处理超 10 亿美元 TVL，支持多链（Ethereum、Base、Polkadot），并与 Polygon CDK 等合作扩展。 以下是其技术路径的完整总结，分为架构、协议、桥接和连接机制。

#### 1. 整体架构（Architecture）

- **基础层**：构建于 Cosmos SDK 和 Tendermint 共识（PoS），兼容 EVM（支持 Solidity 合约）。2025 年 V3 升级引入 zk 证明，提升隐私和可扩展性。
- **多链支持**：通过 IBC（Inter-Blockchain Communication）协议连接 Cosmos 生态，并使用 Wormhole 或 Axelar 桥接到 Ethereum L2（如 Base），实现跨链 RWA 转移。
- **DAO 治理**：社区驱动的 DAO 使用 CFG 代币投票协议参数，类似于 MakerDAO，确保去中心化决策。
- **模块化设计**：分离代币化（off-chain 数据到 on-chain 代币）和池管理（流动性提供），便于扩展。

#### 2. 核心协议（Protocols）

- **资产代币化协议（Asset Tokenization Protocol）**：使用 ERC-721（NFT，非同质化）或 ERC-20（同质化）标准，将 RWA 转化为数字资产。过程包括：线下验证（KYC/法律审计）、元数据上 IPFS、链上铸造。集成 Chainlink Proof of Reserve 预言机，确保资产真实性和 1:1 抵押。
- **池管理协议（Pool Management Protocol）**：基于智能合约的池子模板（如 Tinlake），定义利率、抵押率、投资者权限。支持 DROP（高级份额，低风险）和 TIN（初级份额，高风险）代币模型，实现风险分层。
- **投资者管理协议（Investor Management Protocol）**：处理 KYC、收益分配和争议解决，使用 zk-SNARKs 保护隐私。2025 年新增 AI 优化模块，自动调整池参数。
- **合规层**：内置 Howey Test 兼容框架，确保代币不被视为证券；与传统审计公司合作，提供端到端合规。

#### 3. 桥接机制（Bridges）

- **Off-Chain to On-Chain 桥**：使用 API 和预言机（如 Chainlink）将 TradFi 数据（如发票 PDF）桥接到链上。过程：线下资产 → 数字化（IPFS） → 链上代币。降低传统证券化成本 50%+。
- **流动性桥**：集成 DeFi 协议（如 Aave 通过借贷池），允许 RWA 作为抵押。2025 年与 Morpho 合作，实现动态流动性。
- **跨链桥**：IBC + zk 桥，支持 RWA 在 Ethereum、Solana 等链间流动，避免中心化桥风险。

#### 4. 连接 Off-Chain 资产到 On-Chain 金融（Connecting Off-Chain to On-Chain）

- **TradFi 路径**：资产发行者（企业/基金）通过 Centrifuge API 上传资产，平台处理法律框架（SPV 实体），然后代币化。解锁机构资本（如私募信贷），降低融资成本。
- **DeFi 路径**：代币化 RWA 注入池子 → 投资者 staking/借贷 → 收益分发。类似于 ETF，但去中心化；2025 年目标：10 万亿+ TradFi 资产上链。
- **挑战与解决方案**：波动性（通过稳定抵押解决）、合规（内置审计）、可扩展性（L2 集成）。未来：与 ETF 类似的市场增长。

Centrifuge 通过这些路径，实现了 TradFi 的效率与 DeFi 的透明结合，推动 RWA 市场从 2023 年的 10 亿美元增长到 2025 年的万亿美元规模。 如需代码示例或具体案例，欢迎进一步询问