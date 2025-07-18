# OathLink誓言链-以太坊实现的智能履誓方案

## 核心理念：解决什么问题？

将个人意志力薄弱的问题，通过经济杠杆和社交监督外化为一种硬性约束。

- 场景: 
  - 戒烟
  - 坚持健身
  - 学习打卡
  - 各类短/长期、习惯养成目标...
- 机制:  
  1. **誓言目标**指用户在确认的时间范围内完成指定目标的承诺，用户需锁定一笔**誓言金**到智能合约。  
  2. 每个誓言目标有5席见证人位，私有誓言由用户指定**见证人**，公开誓言由社区自由见证人主动申请，见证人需要通过社区共识认可投票，才可以对誓言结果进行**评判投票**。
  4. 投票数不足5票，本轮评判无效，重新进行投票。
  5. 半数见证人投通过票，则评判结果为成功 -> **本金95%退还给用户，5%分给见证人，并根据当前资金池剩余量1%获得额外奖励**。  
  6. 半数见证人投失败票，则评判结果为失败 -> **本金85%捐给慈善机构，5%分给见证人组（每人1%），8%进入用户奖励资金池，2%进入平台维护资金池**。  
- 目的: 将模糊的个人决心，转化为清晰、有后果的链上誓言，并通过经济模型刺激用户履行誓言，抑制过于简单/困难的誓言，鼓励见证人主动参与给予公正评判，维持用户社区正常运行，且具有一定社交传播效应。

## 经济模型

- 用户奖励
  - 用户因自己每次守誓获得正向激励
  - 引入见证人机制，防止用户通过发布过于简单的誓言目标获利
  - 首次运行时用户奖励资金池为空，需要在Web3社区募集一定量的启动资金
- 见证人奖励
  - 参与评判可以获利，社区鼓励公正的评判
  - 每个誓言目标的见证人组需通过社区投票认证，避免恶意见证人聚集影响结果
  - 评判结果与收益无关，避免为了收益进行恶意评判
  - 恶意评判会影响后续被用户选中的机率从而减少收益
- 公益捐赠
  - 失败的誓言目标会向公益DAO组织进行捐款
- 平台资金池
  - 从失败的誓言目标“誓言金”中少量摄取资金进入平台维护资金池，保证平台健康运行

## 整体技术架构

### 四大核心模块

1. 用户前端 (Frontend)  
  - 与用户交互，管理钱包连接。  
2. 智能合约 (Smart Contract)  
  - 应用的核心逻辑，资金的保险箱和裁判。  
3. 预言机 (Oracle)  
  - 连接链上与链下的桥梁，判定目标是否达成。  
4. 区块链底层 (Blockchain)  
  - 提供去中心化、不可篡改的执行环境。

## 核心模块 1：智能合约设计

### 状态机与核心参数

合约必须是一个定义严谨的状态机。

- 核心状态 (Enum State)  
  - Active (进行中)  
  - Succeeded (成功)  
  - Failed (失败)  
- 初始化参数 (Struct Goal)  
  - owner: address (目标发起人)  
  - stakeAmount: uint256 (誓言金)  
  - deadline: uint256 (截止时间戳)  
  - witnesses: address[] (见证人列表)  
  - failureRecipient: address[] (失败后资金去向)

## 核心模块 1：智能合约设计

资金管理: 誓言金支持ETH和ERC20代币

## 核心模块 2：预言机 (Oracle)

我们需要一个可信的机制，告诉智能合约**用户是否真的完成了目标**。

## 预言机方案 ：社交预言机 (见证人)

- 如何工作: 见证人通过自己的钱包地址调用 castVote() 函数，进行投票。  
- 优点:  
  - 实现简单，无需复杂的链下基础设施。  
  - 适用于无法被量化的目标（如“保持好心情”）。  
- 缺点:  
  - 共谋风险: 发起人可能贿赂见证人（使用社区认证抑制共谋风险）。  
  - 失职风险: 见证人可能忘记投票或丢失私钥（使用重新投票防止失职风险）。  
- 缓解措施: 设置5个见证人并要求多数同意才算通过。

## 核心模块 3 & 4：用户体验与底层选择

在L2网络部署运行，防止过高的GAS吞噬资金

## 技术选型总结

- 区块链平台: 
  - Arbitrum / Optimism (Layer 2)  
- 智能合约:  
  - 语言: Solidity  
- 预言机:  
  - 简单模式: 社交见证人  
- 前端:  
  - 框架: React
  - 钱包: MetaMask
