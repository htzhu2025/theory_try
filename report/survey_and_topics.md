# 联邦式 / 分布式多模态学习：选题调研报告

> **调研条件声明（重要）**
> 本次调研在受限网络环境下完成：出口代理拦截了 arxiv.org / openreview.net / openaccess.thecvf.com /
> proceedings.neurips.cc / huggingface.co / semanticscholar 等站点，仅 WebSearch 与 github.com 可用。
> 因此**所有论文内容来自搜索引擎摘要与 GitHub README，未逐篇核对全文**。
> 下文对每条事实标注置信度：
> - `[已核实]` 多个独立来源一致，且包含可验证的标识（DOI / 会议页 / 代码仓库）
> - `[中等]` 单一来源摘要，方向可信但细节需复核
> - `[假设]` 我的推断，尚无文献支撑，必须做 kill-test
>
> 在动笔写论文前，T1/T2 涉及的每篇关键文献都必须在能联网的机器上读全文复核。

---

## 一、近 5 年研究格局（结构化总结）

按"在解决什么"划分，而非按方法划分。

### 集群 A：缺失模态 / 模态不完整 —— **极度拥挤，已饱和**

这是 2023–2026 年产出量最大的一支，占我检索到的联邦多模态论文的一半以上。

代表工作：CACMRN (ACM MM 2023) `[已核实, DOI 10.1145/3581783.3611757]`、
CAR-MFL (MICCAI 2024) `[中等]`、ClusMFL、FedMAC、FedRecon、FedMVP (2025)、
FediLoRA (2025)、FedTaste (2026)、FedMIR (2026)、FedCoRe (2026)、
Cross-Modal Prototype based MFL under Severely Missing Modality `[中等]`。

方法上收敛成四件套，几乎没有新意：
1. **补全/重建**：用可得模态生成缺失模态（GAN / diffusion / 重建网络）
2. **原型**：共享各模态 prototype，用全局原型顶替缺失模态
3. **适配器 / PEFT 路由**：LoRA、prompt、MoE 路由按模态可得性分派
4. **聚类分组**：按模态组合把客户端分桶再组内聚合

**判断：不要进。** 边际贡献已经逼近零，且新论文互相之间差异很小。唯一残留的价值点见候选选题 T3。

### 集群 B：模态不平衡 / 模态竞争 / modality laziness —— **中度拥挤，但理论侧空白**

中心化侧已有扎实积累：
- OGM-GE, CVPR 2022 Oral `[已核实, GeWu-Lab/OGM-GE_CVPR2022]` —— on-the-fly 梯度调制
- **Modality Competition, ICML 2022 Spotlight (Huang et al., PMLR v162:9226-9259)** `[已核实]`
  —— 唯一的严格理论：证明 late-fusion + (smoothed) ReLU 在联合 GD 下，
  各模态相互竞争，**只有一个子集的模态会被其编码器学到**，输家编码器停留在初始化附近。
- Diagnosing and Re-learning, ECCV 2024 `[已核实, GeWu-Lab]`
- MLA (Alternating Unimodal Adaptation), arXiv 2311.10707 / CVPR 2024 `[中等]`

联邦侧只有算法，没有理论：
- **BMSFed, ECCV 2024** (Fan et al., arXiv 2401.00403) `[已核实]`
- **FedMChain, arXiv 2606.01856 (2026)** `[中等]` —— 链式模态相位优化
- Balance-aware asynchronous aggregation `[中等]`
- Mitigating Modality Quantity and Quality Imbalance in Multimodal Online FL, arXiv 2508.11159 `[中等]`

**判断：这是最有价值的入口。** 见 T1。

### 集群 C：异构模型 / 表示集成 / 知识蒸馏

- **CreamFL, ICLR 2023** (arXiv 2302.08888, FLAIR-THU/CreamFL) `[已核实]`
  —— 提出联邦多模态特有的两个异质性来源：**modality gap** 与 **task gap**；
  用公共数据上的表示做通信，inter-modal / intra-modal 双对比正则。
- **FedCola (Towards Multi-modal Transformers in FL), ECCV 2024** `[已核实, ecva.net]`
  —— 处理单模态客户端的 in-modality gap 与 cross-modality gap。
- FedMMKT (2025)、FedAFD (2026) `[中等]`

### 集群 D：基础模型 + PEFT 联邦 —— **增长最快，也是最大的"工程替代"威胁源**

FedCLIP (2023)、FedMVP (2025)、FedVLM (2025)、FediLoRA (2025)、FedDEAP (2025)、
FedUMM (2026)、Geometric Prior-Guided Federated Prompt Calibration (2025) `[均为中等]`

共同套路：冻结预训练骨干，只联邦少量 prompt / LoRA / adapter。

**这个集群的存在意味着：任何"用冻结 CLIP/ImageBind 就能解决"的选题都不值得做。**
每个候选选题都必须过这一关。

### 集群 E：模态分居不同客户端（VFL / 无配对）

- Cross-Modal Federated Learning among Unimodal Devices, IMWUT 2025 `[已核实, DOI 10.1145/3749467]`
- Federated learning for unpaired multimodal data through a homogeneous transformer,
  arXiv 2601.17986 (2026) `[中等]` —— LoRA + CKA 对齐
- Multimodal FL Survey through the Lens of Different FL Paradigms, arXiv 2505.21792 (2025) `[已核实]`
  —— 按 HFL / VFL / hybrid 三范式组织

### 集群 F：通信效率 / 模态与客户端选择

Communication-Efficient MFL: Joint Modality and Client Selection, arXiv 2401.16685 `[中等]`

### 集群 G：基准

- **FedMultimodal, KDD 2023** `[已核实, usc-sail/fed-multimodal]`
  —— 10 个数据集 / 8 种模态 / 5 类应用；测三种破坏：缺失模态、缺失标签、错误标签。
  数据集：CREMA-D, MELD, UCF-101, MiT-51, UCI-HAR, KU-HAR, CrisisMMD, Hateful-Memes, PTB-XL。
- Med-MMFL (2026)、MM-OpenFGL (2026) `[中等]`

### 集群 H：表示几何与不变性（在 FL 之外，但对 T2 是直接前置）

- **Relative representations enable zero-shot latent space communication, ICLR 2023 Oral (top-5%)**
  `[已核实]` —— 用 anchor 相对表示获得对 latent isometry / rescaling 的不变性
- Sheaf-Based Federated Representation Learning, arXiv 2608.10016 (2026) `[中等]`
  —— 联邦下用 **正交 Procrustes 闭式解** 对齐各客户端 latent space；
  异维用 Stiefel 流形上的半正交映射；证到一阶稳定点收敛。

---

## 二、候选选题

每个选题的"起点异常"都必须是**文献已报告的模型异常**，不是我构造的缺口。

---

### T1 —— 聚合算子与模态竞争的耦合：跨客户端"赢家不一致"如何让全局模型永久丢失一个模态

**推荐度：★★★★★（第一推荐）**

#### 1.1 起点异常（三条，均为已发表工作报告的现象，非构造）

| # | 异常 | 来源 | 置信度 |
|---|------|------|--------|
| A1 | **本地只用单一模态训练，对全局模型的贡献可能超过用全部本地模态训练** | BMSFed, ECCV 2024 (2401.00403) | `[已核实]` 该句是该文摘要表述的转述 |
| A2 | **弱模态在多模态客户端上 loss 停滞，而同一模态在单模态客户端上正常收敛** | FedMChain, 2606.01856 | `[中等]` |
| A3 | **主导模态因客户端而异**（由数据分布、数据质量、模型可得性决定）；同一模态的各本地模型收敛进度差异巨大，这比"更新方向冲突"对聚合的破坏更严重 | FedMChain / 相关 2026 工作 | `[中等]` |
| A4 | 多模态客户端与单模态客户端之间的模型漂移，显著大于同模态客户端之间 | CreamFL, ICLR 2023 | `[中等]` |

A1 尤其关键：它说明**"本地最优"与"全局最优"在模态维度上发生了倒置**。这不是调参问题。

#### 1.2 已有工作在做什么 / 是否拥挤

全部是**算法缓解**：模态选择（BMSFed）、链式相位优化（FedMChain）、
异步 staleness 加权、梯度调制移植、原型增强。

**没有任何工作刻画"平均"这个算子本身如何与模态竞争动力学耦合。**
ICML 2022 的模态竞争理论是单机 GD，没有 averaging，没有本地步数 E，没有客户端异质性。

拥挤度：算法层**中度拥挤**；理论层**空白**。

#### 1.3 真正未解的核心问题

> 模态竞争是一个 **winner-take-all、指数分离** 的动力学过程。
> FedAvg 对已经发生指数分离的量做算术平均。
> 当弱模态只在一部分客户端上"赢"时，平均能否把它救回来？临界比例是多少？它怎么随本地步数变化？

与已有工作的区别：已有工作问"怎么让弱模态别输"；这个选题问
**"在什么 (α, E) 条件下弱模态在全局层面可证地不可恢复"** —— 一个不可能性/相变结论。

#### 1.4 证据等级

A1 是已发表的实证观察 `[已核实来源，具体数字未核]`。
A2/A3 是 2026 preprint 的观察 `[中等]`。
**相变的存在本身是 `[假设]`** —— 见 4.3 / 4.4 的判据。

#### 1.5 工程替代风险：**中**（可控，但必须正面打掉）

| 工程方案 | 是否能消除 | 依据 |
|---|---|---|
| 冻结预训练编码器 + 线性探测 | **否** | 检索证据显示模态不平衡在 frozen / linear-probe 设置下依然存在，且已在较小的融合模型中通过 probing/freezing 被确认 `[中等]`。另有报道：即使用了 OGM-GE，多模态模型内的单模态性能仍不超过最佳单模态模型 `[中等]` |
| 按模态分组聚合 | **否，它本身就是相图上的一个点** | 分组聚合 = 令 α 对每组为 1，正是理论要预测的极限情形 |
| MLA 式交替单模态训练 | **部分** | 必须作为主基线打；理论应能预测它在相图哪个区域失效 |
| 加大公共数据 / 蒸馏 | **部分** | CreamFL 路线，但需要公共数据，且不改变本地竞争动力学 |

**必须做的防守实验**：用冻结 CLIP / ImageBind 特征重跑相图。若边界消失 → 直接 NO-GO。

#### 1.6 理论空间：**强**

可攻的命题（沿用 ICML 2022 的 late-fusion + 两层网络设定，加上 FedAvg 算子）：

- **上界**：α > α*(E) ⇒ 全局模型在 T 轮内学到弱模态，给出 T 的界
- **下界 / 不可能性**：α < α*(E) ⇒ 对任意轮数，弱模态编码器始终停留在初始化的 O(·) 邻域
- **相变形式**：猜想 α*(E) = 1 − c·e^(−λE)，即**本地步数越多，救回弱模态所需的"赢家客户端比例"越高**
- 直觉机制：本地 E 步内竞争已指数分离；对指数分离后的量做算术平均，Jensen 方向不利；
  E→0 退化为中心化 SGD（无相变），E→∞ 退化为 one-shot 集成（相变阈值→1）

这是收敛界 + 相变 + 不可辨识性三者兼具的形态，符合 TPAMI 的体量。

#### 1.7 实验现象：**强、直观、不是涨点**

核心产出不是表格，是**一张二维相图**：
- 横轴 α = 弱模态在其上占优的客户端比例
- 纵轴 E = 本地步数
- 颜色 = 全局编码器的**弱模态线性探测准确率**（冻住全局编码器，只训单模态线性头）

预期：出现**锐利边界**，而非平滑梯度。

> 关键设计：读数用 **per-modality linear probe**，不是最终融合准确率。
> 这样测的是"全局模型是否把该模态编码进了表示"，
> 与已排除的"多模态 vs 单模态谁准确率高"完全是两个量。**这条必须守住。**

#### 1.8 撞题风险

- BMSFed (ECCV 2024) 与 FedMChain (2026) 是算法论文，不构成撞题，反而是 motivation 来源与基线。
- 未检索到联邦版模态竞争理论。
- **主要风险是时间**：ICML 2022 那篇理论已 4 年，联邦扩展是个明显的下一步，可能有人在做。
  → 缓解：尽快把相图做出来占住"现象"，理论后补。现象本身就可发。

#### 1.9 单机可行性：**极好**

- 数据：CREMA-D（音+视，~7.4k，公开）、Kinetics-Sounds 子集 / AVE、UCI-HAR（加速度+陀螺，极小）、CMU-MOSEI（有现成预提取特征）
- 预提取特征后落盘 `.npy`，训练只涉及两个 2 层 MLP 编码器 + late fusion 头
- 单 run 数秒到数十秒；单进程串行模拟 20–50 客户端
- M5 Mac 足以在 1–2 天内扫完 20×7 网格 × 3 种子 × 2 数据集

---

### T2 —— 联邦跨模态对齐的不可辨识性（isometry 模糊 + 模态分居客户端）

**推荐度：★★★（第二梯队，风险高）**

#### 起点异常
- 对比学习的潜空间只在正交变换意义下可辨识 —— 这正是 Relative representations (ICLR 2023 Oral)
  与 Sheaf-FRL (2026, 用正交 Procrustes 闭式解) 的前提 `[已核实/中等]`
- "CLIP 编码器本地微调后，各客户端的 embedding 漂进各自的空间" `[中等]`
- CreamFL 明确把 **modality gap** 列为联邦多模态特有的异质性来源 `[已核实]`
- Mind the Gap (NeurIPS 2022)：独立初始化的编码器把输出限制在超球面上几何分离的锥体内，
  对比损失**保持**而非消除这一分离 `[已核实]`

#### 核心问题
当模态分居不同客户端（A 只有图、B 只有文，从无配对样本），跨模态对齐在什么条件下可辨识？
需要多少 anchor 才能把两个空间钉死？anchor 数是否存在相变？

#### 理论空间：**很强**
可辨识性、Procrustes 对齐的 sample complexity、anchor 数的相变、无配对情形的不可能性定理。

#### 致命风险（两条，所以排在 T1 之后）

1. **工程替代风险：高。** 直接用冻结的 CLIP / ImageBind 作为所有客户端的共享坐标系，
   rotation 模糊性直接消失。审稿人第一个问题就是这个。
   问题只在**没有预训练对齐编码器的模态对**（医学影像+病理、异构传感器）才真实存在——
   而那些数据集（ADNI、MIMIC）需要资质申请，与"公开可获取 + M5 单机"冲突。
2. **撞题风险：高。** Relative representations (ICLR 2023 Oral)、Sheaf-FRL (2026)、
   FedCola (ECCV 2024)、2601.17986 (2026) 已经占住了大部分地形。
   Sheaf-FRL 尤其接近：它已经在联邦下做了 Procrustes 对齐并给了收敛性。

#### 单机可行性
好（小模型 + 预提取特征即可），但数据可得性是瓶颈。

**结论：除非能拿到一个没有预训练对齐编码器的公开模态对，否则不建议作为主线。**

---

### T3 —— 缺失模态机制的错配：现有方法全部建立在 MCAR 模拟之上

**推荐度：★★★（适合做成一篇"揭穿 + 基准"论文，不适合做 TPAMI 主线理论）**

#### 起点异常（这是一条方法论异常，证据等级较高）
检索证实：缺失模态 FL 工作的标准模拟协议是
**"为每个客户端的每个模态设定固定缺失率，按 Bernoulli 分布丢弃"，缺失率取 10%/20%/30%/40%/50%** `[中等，多来源一致]`。
即整个子领域的实验基础是 **MCAR（完全随机缺失）**。

而现实中缺失几乎总是信息性的：医院对轻症不做 MRI，低端设备缺高耗电传感器。
已有工作明确区分 random (MCAR) / block (MAR) / informed (MNAR) 三类，
并指出 **MNAR 在临床上普遍存在** `[中等]`。

#### 核心问题
当缺失与标签/客户端相关（MNAR）时，补全类方法（imputation / prototype / reconstruction）
会引入**系统性偏差**，而不只是精度下降；且该偏差可能随客户端数放大而不是被平均掉。

#### 与硬约束冲突之处（必须坦白）
要求是"不要人为构造"。而 MNAR mask 是**构造出来的**。证据等级因此弱于 T1。

可以辩护的框架是：
> 异常不在于"构造了一个难题"，而在于
> **"现有方法报告的增益，在一个同样合理的缺失机制下消失甚至反向"** ——
> 这是对既有模型的证伪，不是新造一个问题。

这个框架成立，但说服力低于 T1 的"文献已报告的异常"。

#### 理论空间：中等偏强
MNAR 下的可辨识性 / 部分辨识（partial identification）、补全偏差的下界。
但这套理论在因果推断/缺失数据文献里已经很成熟，移植到联邦多模态的原创性有限。

#### 工程替代风险：中
若有充足公共数据做无偏校准，偏差可被大幅削减。

#### 可行性：极好（mask 自造，计算成本最低）

**定位建议：作为 T1 的配套第二篇，或者作为进入这个领域的第一篇练手/占位论文。**

---

### T4 —— 涌现式跨模态捷径：聚合造出一个任何单一客户端都没有的 shortcut

**推荐度：★★（目前纯假设，但 kill-test 极便宜，值得花两天验一下）**

#### 假设内容 `[假设 —— 无文献支撑，务必先做 kill-test]`
各客户端有各自不同的"模态—标签"伪相关。若这些伪相关在表示空间存在一个**共享的 nuisance 方向**，
梯度平均会在该方向上**相干叠加**（各客户端的信号方向互相抵消，nuisance 方向同相累加），
于是全局模型依赖一个**任何单一客户端本地模型都不依赖的捷径**。

#### 为什么可能真实
FL 里已有 shortcut 学习的研究（Reducing Spurious Correlation for Federated Domain Generalization,
2407.19174；Causally Motivated Personalized FL, ICML 2024；FuseFL, NeurIPS 2024 从因果视角看 one-shot FL）`[中等]`，
但**没有检索到**"捷径由聚合涌现、且不存在于任何客户端"这一具体主张。

#### 风险
1. 可能已被 federated domain generalization 覆盖 → 需要更仔细的文献核查
2. 可能根本不成立（相干叠加需要很强的条件）
3. 多模态成分可能是多余的 —— 若在单模态 FL 就成立，就不是多模态选题

#### 最低成本 kill-test（约 2 天）
用 Colored-MNIST 风格的双模态合成数据：模态 1 带客户端特异的颜色捷径，模态 2 带真实信号。
构造 K 个客户端，每个客户端的捷径方向不同但共享一个低维子空间。
训练后测：全局模型在"捷径子空间"上的依赖度是否**高于任何本地模型**。
若否 → 直接砍掉。

---

## 三、排序与推荐

| 排名 | 选题 | 异常证据等级 | 理论空间 | 现象强度 | 工程替代风险 | 撞题风险 | M5 可行性 |
|---|---|---|---|---|---|---|---|
| **1** | **T1 聚合 × 模态竞争的相变** | **高**（ECCV24 + 2026 已报告） | **强** | **强（相图）** | 中 | 中（时间压力） | **极好** |
| 2 | T3 MNAR 缺失机制错配 | 中（方法论异常，mask 自造） | 中偏强 | 中 | 中 | 低 | 极好 |
| 3 | T4 涌现式捷径 | **低（纯假设）** | 中 | 潜在强 | 中 | 未知 | 极好 |
| 4 | T2 跨模态对齐不可辨识性 | 高 | **强** | 中 | **高** | **高** | 中（数据瓶颈） |

### 最推荐：**T1**，配套 **T3** 作为第二篇

理由（按给定标准逐条回答）：

- **问题是否真实**：是。起点是 ECCV 2024 已发表的实证倒置（A1）与 2026 工作报告的收敛停滞（A2/A3），
  不是构造的缺口。
- **是否重要**：是。它决定"联邦多模态模型到底学没学到某个模态"，是该领域所有算法工作的共同底座；
  BMSFed / FedMChain 都在绕着它做工程，却没人说清楚机制。
- **是否值得投入**：是。有三层递进的产出——
  (i) 相图现象（可独立发 CVPR/ICML 级）；
  (ii) 相变定理（TPAMI 的核心）；
  (iii) 由理论导出的聚合策略（算法贡献）。
  而且这三层任意一层单独失败，其余两层仍可成文。
- **为什么不是 T2**：T2 理论更漂亮，但工程替代与撞题两个风险同时为高，且数据不满足硬件/公开性约束。
  漂亮的理论配不上真实的问题就是自杀。

### 与已排除方向的边界（请复核这一条）

已排除"多模态不如少模态 / 加模态反而掉点"。
T1 的异常 A1（单模态本地训练对全局贡献更大）**在表述上接近**这条排除线。

我认为它不同：A1 比较的是**联邦聚合下不同本地训练配置对同一个全局多模态模型的贡献**，
不是比较多模态模型与单模态模型的推理性能。

**为了把这条守住，T1 的主读数必须是 per-modality linear probe，而不是最终融合准确率。**
如果你认为这仍然踩线，T3 应提为第一推荐。

---

## 四、T1 最小可行现象验证方案（MVP）

### 4.1 设计

**数据**（全部公开、全部可预提取特征）
- 主：CREMA-D（音频 + 视频，~7.4k clips）
- 副：Kinetics-Sounds 子集 或 AVE
- 控制组（极小、纯数值）：UCI-HAR（加速度 + 陀螺）

**特征**：一次性用冻结编码器提取并落盘 `.npy`
- 音频：VGGish 或 AST
- 视觉：ResNet-18 或 CLIP ViT-B/32
- 之后所有实验只训练：2 个 2 层 MLP 编码器 + late-fusion 线性头

**联邦模拟**：单进程串行，K = 20~50 客户端，Dirichlet(β) 非 IID 标签划分

**两个受控自变量**
- `α` ∈ [0, 1]：弱模态在其上占优的客户端比例。
  通过给不同客户端的强模态注入不同强度的噪声/降采样来实现（**不改标签**）。
  每个客户端的"谁占优"先用单模态本地训练**实测标定**，而不是假定。
- `E` ∈ {1, 2, 5, 10, 20, 50, 100}：本地步数

**核心读数（不是准确率）**
冻住全局编码器 → 只在其上训一个单模态线性头 → 得到该模态的 **linear probe accuracy**。
这直接测量"全局表示里有没有这个模态的信息"。

### 4.2 必跑的对照

| 对照 | 目的 |
|---|---|
| K=1、总步数相同的集中式训练 | 证明边界是**联邦特有**，不是 ICML 2022 结论的复述 |
| 冻结 CLIP / ImageBind 特征 | 打掉最大的工程替代 |
| MLA 式交替单模态训练 | 打掉最强的算法基线 |
| 按模态分组聚合 | 验证它对应相图上 α=1 的极限 |
| BMSFed 复现 | 定位已有方法在相图哪个区域有效 |

### 4.3 GO 判据（全部满足才继续）

1. **存在临界 α\*(E)**：边界两侧弱模态 linear probe 准确率差 **≥ 15 个百分点**，
   且过渡区宽度 **< 0.1**（以 α 计）。
2. **α\*(E) 随 E 单调上升**，在 **≥ 2 个数据集**、**≥ 3 个随机种子** 上方向一致。
3. **集中式对照不出现该边界** —— 这是"联邦贡献"的存亡判据。
4. **冻结 CLIP/ImageBind 后边界仍在** —— 工程替代未能消除。

### 4.4 NO-GO 判据（任一触发即止损）

1. 过渡是平滑单调的，无锐利边界 → 没有相变，理论卖点消失，降级为调参工作 → **停**。
2. 集中式对照出现相同边界 → 是 ICML 2022 已有结论的重复 → **停**。
3. 冻结预训练特征后边界消失 → 工程替代成立 → **停**。
4. 两个数据集上 α\*(E) 随 E 的方向不一致 → 证据不足，不要硬拗 → **停**，退回 T3。

### 4.5 时间与算力预算

| 阶段 | 内容 | 预算 |
|---|---|---|
| 1 | 特征预提取并落盘 | 1–2 天 |
| 2 | 联邦模拟骨架 + 客户端占优模态标定 | 2–3 天 |
| 3 | 相图扫描：20(α) × 7(E) × 3 seed × 2 数据集 ≈ 840 runs | ~20 机时，M5 上 1–2 天 |
| 4 | 四组对照 | 2–3 天 |
| **合计** | **≤ 2 周内可以判死或判活** | |

这个预算是选择 T1 的一大理由：**证伪成本极低**。

### 4.6 若 GO，理论要证的三件事

1. **上界**：α > α\*(E) ⇒ 全局模型在 T 轮内学到弱模态，给出 T 的显式界
2. **下界/不可能性**：α < α\*(E) ⇒ 对任意轮数，弱模态编码器停留在初始化的 O(·) 邻域
3. **相变形式**：α\*(E) = 1 − c·e^(−λE)，并在 E→0 时退化为中心化（无相变）、E→∞ 时阈值→1

设定沿用 Huang et al. (ICML 2022) 的 late-fusion + 两层 (smoothed) ReLU + 高斯 latent 信号，
外加 FedAvg 算子与客户端信噪比异质性 σ_{k,m}。

---

## 五、下一步必须做的核查（在写论文前）

1. **在能联网的机器上读全文**：BMSFed (2401.00403)、FedMChain (2606.01856)、
   Huang et al. ICML 2022 (PMLR v162)、CreamFL (2302.08888)、
   "Tackling Modality-Heterogeneous Client Drift Holistically"（PubMed 40030772，疑似 TPAMI 2025，**未核实**）。
2. **核对 A1 的原始实验设置与数字** —— 本报告只转述了摘要级表述。
3. 检索 2026 下半年是否已有"联邦模态竞争理论"论文（本次检索未发现，但时间风险实在）。
4. 确认 T1 的读数设计是否被认为踩到已排除方向的线。

---

## 参考文献（本次检索到、置信度较高者）

- Huang et al. *Modality Competition: What Makes Joint Training of Multi-modal Network Fail in Deep Learning? (Provably)*. ICML 2022, PMLR v162:9226-9259.
- Peng et al. *Balanced Multimodal Learning via On-the-fly Gradient Modulation*. CVPR 2022 (Oral).
- Liang et al. *Mind the Gap: Understanding the Modality Gap in Multi-modal Contrastive Representation Learning*. NeurIPS 2022.
- Yu et al. *Multimodal Federated Learning via Contrastive Representation Ensemble (CreamFL)*. ICLR 2023. arXiv:2302.08888.
- Feng et al. *FedMultimodal: A Benchmark For Multimodal Federated Learning*. KDD 2023. arXiv:2306.09486.
- Moschella et al. *Relative representations enable zero-shot latent space communication*. ICLR 2023 (Oral, top-5%). arXiv:2209.15430.
- *Client-Adaptive Cross-Model Reconstruction Network for Modality-Incomplete Multimodal Federated Learning*. ACM MM 2023. DOI 10.1145/3581783.3611757.
- Fan et al. *Overcome Modal Bias in Multi-modal Federated Learning via Balanced Modality Selection (BMSFed)*. ECCV 2024. arXiv:2401.00403.
- *Diagnosing and Re-learning for Balanced Multimodal Learning*. ECCV 2024.
- *Towards Multi-modal Transformers in Federated Learning (FedCola)*. ECCV 2024. arXiv:2404.12467.
- *Robust Multimodal Learning With Missing Modalities via Parameter-Efficient Adaptation*. TPAMI 2024. DOI 10.1109/TPAMI.2024.3476487.
- *Cross-Modal Federated Learning among Unimodal Devices*. IMWUT 2025. DOI 10.1145/3749467.
- *Multimodal Federated Learning: A Survey through the Lens of Different FL Paradigms*. arXiv:2505.21792 (2025).
- *Boosting Multimodal Federated Learning via Chained Modality Optimization (FedMChain)*. arXiv:2606.01856 (2026), preprint.
- *Sheaf-Based Federated Representation Learning*. arXiv:2608.10016 (2026), preprint.
- *Towards Optimal Multi-Modal Federated Learning on Non-IID Data*. INFOCOM 2022.
