# Sigma Session: CMIS 协议 (OIF-CMIS 05.3)

## 学习者画像 (Learner Profile)
- **起点水平**: 零基础（不熟悉光模块/硬件管理接口）— 2026-10-20 诊断
- **学习目标**: 光模块 Firmware 开发
- **时间投入**: 5 天/周 × 2 小时 = 10 小时/周
- **会话语言**: zh（技术术语保留英文）
- **术语教学规则**: 技术术语第一次出现时必须先解释；缩略词须给出英文全称、中文含义、用途/所属层次，必要时说明与相近术语的区别。不得在未定义前直接用缩略词讲解或出题。
- **教材**: `OIF-CMIS-05.3-changebars.pdf`（408 页，Rev 5.3, 2024-09）
- **方法**: Bloom 2-Sigma 掌握学习 — 概念掌握度 ≥80% 才进入下一概念

## 总体进度 (Overall)
- **已掌握**: 9 / 16 概念 (57% 综合进度)
- **当前概念**: M10 诊断：VDM 与性能页
- **当前周次**: Week 7 / 10
- **累计提问**: 104 轮
- **会话状态**: paused（今日课程结束）
- **恢复入口**: M10 VDM Descriptor 场景题：读取 BER 前先遍历 Descriptor Array，定位 Observable 类型为 BER 的 Active Instance，再读取对应 Sample

## 概念清单与掌握度 (Concepts)

| # | 概念 | 对应章节 | 预估 | 状态 | 掌握度 |
|---|------|---------|------|------|--------|
| M1 | 光模块与 CMIS 全景 | §1, §4.1-4.2, App A | 4h | mastered | 85% |
| M2 | 术语、数据类型与数据操作 | §2.3, §3 | 4h | mastered | 95% |
| M3 | 管理接口分层：MSL 与 RAL | §5 | 5h | mastered | 95% |
| M4 | 功能模型：Application 与 Data Path | §6.1-6.2.2 | 6h | mastered | 95% |
| M5 | Control Set 与配置流程 | §6.2.3-6.2.4, App D/E | 6h | mastered | 92% |
| M6 | 模块状态机 MSM ⭐ | §6.3 | 8h | mastered | 96% |
| M7 | 内存映射机制：Lower Memory 与分页/Banked | §8.1-8.2 | 5h | mastered | 92% |
| M8 | 管理页：Page 00h-05h | §8.3-8.8 | 5h | mastered | 92% |
| M9 | Lane/Datapath 控制与状态 10h/11h/18h/19h | §8.9-8.10, §8.17-8.18 | 6h | mastered | 92% |
| M10 | 诊断：VDM 与性能页 13h/14h | §7.1, §8.12-8.13, §8.22 | 6h | in-progress | 18% |
| M11 | CDB 消息机制 | §7.2, §8.23-8.24, §9.1-9.2 | 5h | not-started | 0% |
| M12 | CDB 命令组精讲（含固件管理） | §9.3-9.12 | 8h | not-started | 0% |
| M13 | 高级特性：MBR/可调激光器/热重构等 | §7.3-7.8 | 6h | not-started | 0% |
| M14 | 时序规范与 MCI 物理层 | §10, App A-B | 5h | not-started | 0% |
| M15 | 实战串讲：初始化/诊断/网络路径示例 | App D-H | 5h | not-started | 0% |
| M16 | 综合考核与跨版本兼容 | App G + 全书 | 4h | not-started | 0% |

## 周计划 (Weekly Schedule)
- **Week 1**: M1 + M2（全景与术语）
- **Week 2**: M3 + M4（接口分层 + 功能模型）
- **Week 3**: M5（Control Set 与配置流程）
- **Week 4**: M6（状态机 MSM ⭐难点周）
- **Week 5**: M7 + M8（内存映射机制 + 管理页）
- **Week 6**: M9（Lane/Datapath 控制与状态）
- **Week 7**: M10 + M11（VDM 诊断 + CDB 机制）
- **Week 8**: M12（CDB 命令组，最大章）
- **Week 9**: M13 + M14（高级特性 + 时序/物理层）
- **Week 10**: M15 + M16（实战串讲 + 综合考核）

## FW 开发视角重点标注
- 核心必精: M5, M6, M7, M9, M11, M12（FW 日常工作面）
- 参考查阅: M8, M10, M13, M14（开发时按需深查）
- 出题偏好: 多用"寄存器读写预测 / 状态机转移推演 / 初始化时序排错"类题型
- 教学偏好: 即使基于直觉的回答全部正确，也必须继续讲解概念定义、工程原因、反例与边界条件，并使用新场景验证迁移理解；不能只凭选择正确快速推进或判定掌握。

## 文档职责与持续维护规则
- `session.md`: 保存学习状态、掌握度、逐轮回答与纠错轨迹。
- `review-notes.md`: 保存整理后的长期复习知识库，是主要复习资料。
- `roadmap.html`: 保存实时路线、模块状态与总体进度。
- `summary.html`: 保存阶段性学习快照。
- **强制同步规则**：后续每轮学习在更新 `session.md` 与 `roadmap.html` 的同时，都必须检查并同步更新 `review-notes.md`，不得只记录分数或会话日志。
- 每完成一个知识点，`review-notes.md` 至少记录定义、工程原因、边界/反例、Firmware 实现提示和自测要点；重要误区必须加入“个人易错点清单”。
- `review-notes.md` 中新增任何自测题时，必须在该组题目正下方提供可折叠的逐题参考答案与解析；折叠块内部必须使用自包含 HTML，不能在 HTML 容器中混用 Markdown 标记。
- 若本轮只是重复验证、没有新增知识，也应检查复习文档；只有确认无新增内容时才可不修改，并在内部保持该判断。
- 恢复会话时，除读取当前概念和恢复入口外，还必须确认 `review-notes.md` 已覆盖上一轮已教授的内容。

## 会话日志 (Session Log)
- **2026-10-20**: 建档。诊断=零基础；目标=光模块FW；节奏=10h/周；生成 roadmap.html；进入 M1。
- **2026-10-20 · M1 Round 1**: 能准确指出管理通道用于读取模块状态、告警、厂商信息、FW 版本，并可执行复位等控制操作。评价：核心直觉正确；下一步辨析高速数据通道与低速管理通道的职责边界。M1=25%。
- **2026-10-20 · M1 Round 2**: 正确区分高速网络数据属于 Host/Media 高速通道，温度读取及复位属于 CMIS 管理通道；尚未解释两类通道必须分离的原因。评价：分类正确，因果理解待验证。M1=40%。
- **2026-10-20 · M1 Round 3**: 正确判断模块启动时管理通道应先可用，且高速链路故障后管理通道仍须工作。评价：已理解管理通道不能依赖业务数据通道的基本原因；下一步验证 FW 启动流程中的实际应用。M1=55%。
- **2026-10-20 · M1 Round 4**: 排序为 A→C→D→E→B，并总结“管理通道优先、Host 主动请求、Module 响应”。原则理解正确；但 E（按配置建立高速数据通道）先于 B（启动高速收发电路）存在依赖关系疑点，需自行修正。M1=65%。
- **2026-10-20 · M1 Round 5**: 修正为 A→C→D→B→E，并指出可工作的数据通道依赖高速电路具备工作条件。评价：依赖关系理解正确；需注意这只是当前抽象模型，后续将用 CMIS Control Set 与 Data Path State Machine 精确定义配置、应用和硬件启动的真实关系。进入 M1 综合掌握检查。M1=75%。
- **2026-10-20 · M1 Round 6（掌握检查）**: 正确判断高速业务不通不代表模块整体损坏，并识别 Host=交换机、Module=光模块；但认为模块“没有响应、未进入可管理状态”，与主机已成功读取厂商/FW/温度这一条件矛盾，且尚未区分管理接口、Host 高速接口和 Media 高速接口。M1 回调至 70%，进行针对性补强。
- **2026-10-20 · M1 Round 7（补强验收）**: 正确配对 Host 高速接口→交换机 ASIC、Media 高速接口→光纤/远端、CMIS 管理接口→交换机管理控制器。M1 最终掌握度 85%，状态 mastered；进入 M2。
- **2026-10-20 · M2 Round 1（起点诊断）**: 将题目 `0x05` 误读为 `0x50`；对实际计算的 `0x50` 能得到 `0b1010000`，并按零基位号识别 bit 4/6。暴露的核心缺口是高低十六进制半字节的位置与固定 8-bit 补零表示。M2=10%。
- **2026-10-20 · M2 Round 2**: 正确得到 `0x05 = 0000 0101`，识别置位为 bit 0 与 bit 2，并映射为温度告警和通信告警。高低半字节、零基位编号与基本位字段读取已掌握。M2=30%。
- **2026-10-20 · M2 Round 3**: 理解使用按位 AND 掩码隔离目标位，但选择 `0x02` 读取 bit 2，发生零基位号偏移一位；`0x02` 实际对应 bit 1。需用 `mask = 1 << bit_index` 规则修正。M2=35%。
- **2026-10-20 · M2 Round 4**: 在表达式中正确填写 `1U << 2`，但仍选择 A (`status & 0x02`)，两个答案相互矛盾。说明已记住目标位号，但尚未稳定完成“位号→移位值→十六进制掩码”的转换。M2=38%。
- **2026-10-20 · M2 Round 5**: 正确完成 `1U << 2 = 0000 0100 = 0x04`，并选择 B。已建立“bit index→掩码”的稳定映射，接下来练习 SET/CLEAR 位操作。M2=50%。
- **2026-10-20 · M2 Round 6**: 清除 bit 1 正确选择 `control &= ~(1U << 1)`；设置 bit 2 误选 AND。说明已掌握 CLEAR 模式，但需区分 OR 的“保留并置位”与 AND 的“过滤保留”。M2=55%。
- **2026-10-20 · M2 Round 7**: 正确判断 OR 结果能够保留 bit 1 并设置 bit 2，最终选择 `control |= (1U << 2)`。SET/CLEAR 单 bit 操作已掌握，转入多 bit 字段提取。M2=68%。
- **2026-10-20 · Session Pause**: 学习者主动结束今日课程。已掌握 M1（85%）；M2 当前 68%。下次从连续位字段 `bits 4:2` 的掩码、右移与枚举值解释继续。
- **2026-10-21 · Session Resume**: 恢复 M2（68%）。按学习者要求，先复习连续字段的掩码生成、右移对齐和枚举状态解释。
- **M2 Round 8（复习）**: 正确生成 `bits 4:2` 掩码 `0001 1100 = 0x1C`，并正确判断字段宽度为 3；将最低位编号误写为 1，尚未完成通用公式代入。M2=72%。
- **M2 Round 9（复习）**: 正确计算 `(1U << 3) - 1U = 0b111`；将左移量回答为最高位编号 4，而不是最低位编号 2。需强化“字段最低位对齐到目标最低位”的规则。M2=74%。
- **M2 Round 10（复习）**: 已正确修正左移量为最低位编号 2，即 `((1U << 3) - 1U) << 2`；尚未给出最终二进制结果，待完成公式验证。M2=76%。
- **M2 Round 11（复习）**: 正确计算 `0000 0111 << 2 = 0001 1100`，完整掌握 `bits 4:2` 掩码生成：字段宽度 3、最低位 2、公式 `((1U << 3) - 1U) << 2 = 0x1C`。进入字段右移与枚举解释。M2=80%。
- **M2 Round 12（复习）**: 正确判断掩码 AND 后字段仍位于原始 `bits 4:2`，并未自动移动至 `bits 2:0`；尚未给出 AND 的具体二进制结果。M2=82%。
- **M2 Round 13（复习）**: 将 `1011 0100 & 0001 1100` 计算为掩码自身 `0001 1100`，进而右移得到 `0000 0111`。暴露出“mask 的 1 允许原 bit 通过，而非强制置 1”的混淆；需重新逐位计算 bits 4:2。M2 回调至 80%。
- **M2 Round 14（复习）**: 正确计算 `1011 0100 & 0001 1100 = 0001 0100`，再右移两位得到 `0000 0101`。已理解 AND 用于过滤无关位，右移用于将字段最低位从 bit 2 对齐到 bit 0。进入枚举解释。M2=85%。
- **M2 Round 15（枚举解释）**: 正确将 `0b101` 映射为 Powering Down；但认为规范标注的 `0b111 Reserved` 可由厂商自定义。需区分 Reserved（保留给规范未来使用/不得私自赋义）与 Vendor Specific（明确允许厂商定义）。M2 回调至 82%。
- **M2 Round 16（复习验收）**: 正确判断私自使用 Reserved 会造成 Host/Module 语义冲突，并选择“不自行赋义，除非后续规范明确规定”。三项复习目标已通过：掩码生成、右移对齐、枚举解释。继续 M2 的规范关键字与数据语义。M2=86%。
- **M2 Round 17（规范关键字）**: 正确判断 `shall` 表示强制要求；未实现 `shall` 不能视为符合该项规范。尚需区分 `should` 与 `may`。M2=88%。
- **M2 Round 18（规范关键字）**: 正确说明 `should` 是强烈推荐但可在有充分理由时偏离，`may` 是无偏好的自由选择；正确判断特殊安全条件下不重试不一定违规、未实现可选 VDM 不违规。M2=92%。
- **M2 Round 19（掌握验收）**: 正确判断未实现 Optional 功能可以合规；一旦宣称支持，就必须遵守该功能的寄存器、状态和流程规范。M2 最终掌握度 95%，状态 mastered；进入 M3。
- **M3 Round 1（起点诊断）**: 四项分类全部正确：专用复位/中断引脚属于 MSL，寄存器温度读取/控制写入属于 RAL；能概括 MSL=离散硬件信号、RAL=寄存器访问。M3=25%。
- **M3 Round 2**: 正确判断无论物理上低有效还是高有效，请求复位在 CMIS 抽象语义中均为 ASSERTED；尚未解释为何不将 ASSERTED 固定为高电平。需理解逻辑功能语义与封装电气实现的解耦。M3=35%。
- **M3 Round 3**: 正确选择 A/B：若将 ASSERTED 固定成高电平，低有效封装将无法正确表达功能生效，并与 CMIS 功能语义冲突。尚需明确说出 ASSERTED 描述功能状态而非物理电压。M3=45%。
- **M3 Round 4**: 正确确认 ASSERTED 描述某项管理功能正在被请求或生效，而不是固定的物理高/低电平。已理解 MSL 逻辑语义与封装电气实现解耦。M3=55%。
- **M3 Round 5（已纠正）**: READ 与 WRITE 分类正确。导师错误地将 TEST 描述为检查寄存器指定位，学习者据题意作答；经核对 §5.2.2.3，TEST 实际检查模块是否准备好接受下一次 READ/WRITE。该错误不归因于学习者，M3 调整为 60%，重新学习 TEST 与 Access Hold-Off。
- **M3 Round 6**: 正确说明 TEST 检查模块是否准备好接受 RAL 的 READ/WRITE；TEST=false 时 Host 应等待后重试，而非强制访问。TEST 与访问就绪语义已掌握。M3=70%。
- **M3 Round 7**: 正确分类：内部处理导致的临时拒绝属于 Access Hold-Off；写只读寄存器属于访问方法不合法，等待不能解决。对超时必要性的回答集中于“无法立即判断临时或永久”，尚需推导超时用于防止异常模块永久阻塞 Host。M3=75%。
- **M3 Round 8**: 正确说明最大重试/超时用于在模块长期不恢复时终止当前操作并向上层报告故障。Access Hold-Off 的重试边界已理解；进入多字节数据一致性。M3=80%。
- **M3 Round 9**: 正确识别分字节读取动态 16-bit 计数器可能破坏原子性/一致性；当规范保证 size-matched 2-byte READ 原子时，应一次读取两个字节，避免拼接出从未存在的混合值。M3=88%，进入综合验收。
- **M3 Round 10（综合验收）**: 正确给出 B→C→D→A→E：先识别 MSL Interrupt ASSERTED，再通过 RAL TEST/重试等待访问就绪，使用一次 2-byte READ 保证一致性，最后解析数值。M3 最终掌握度 95%，状态 mastered；进入 M4。
- **Milestone 1**: 已掌握 M1-M3，生成基础概念图 `concept-map/cmis-foundations.excalidraw`。
- **2026-10-21 · Midday Pause**: 上午课程结束。M3 已掌握，M4 尚未作答。下午从 Application、Application Instance、Data Path 三者关系的起点诊断继续。
- **2026-10-21 · Afternoon Resume**: 恢复 M4 起点诊断。
- **M4 Round 1（起点诊断）**: 三项判断全部正确：400G/100G 是 Application 定义；4 个并行 100G 是同一 Application 的 4 个 Application Instance；每个实例占用的 lanes 与内部资源构成独立 Data Path。M4=25%。
- **M4 Learning Adjustment**: 学习者说明 Round 1 主要凭直觉作答，尚未理解概念依据。M4 保持 25%，不将该轮视为稳定掌握；后续改为慢速推导：先 Application vs Application Instance，再 Lane，最后 Data Path。
- **M4 Round 2（概念重建）**: 正确分类：能力描述“100G、两条 Host Lane”是 Application；当前运行在 Host Lane 3-4 的 100G 是 Application Instance。尚未说明 Instance 与具体资源绑定的原因。M4=30%。
- **M4 Round 3（概念重建）**: 正确指出 Application Instance 已绑定具体 Lane；第二空回答“资源”，重复了资源绑定维度，尚未识别每个实例还具有独立运行状态。M4=33%。
- **M4 Round 4（概念重建）**: 正确说明 Application Instance 绑定具体 Lane，并具有独立配置和运行状态；已能解释同一 Application 的多个实例可处于不同阶段。M4=42%。
- **M4 Round 5（Data Path）**: 正确选择 Data Path 包含 Application Instance 使用的 Host Lane、Media Lane 和所需内部资源；能说明实例关闭时必须成组释放全部关联资源，不能只释放 Host Lane。M4=55%。
- **M4 Round 6（Lane 映射）**: 正确判断该 Data Path 包含 4 条 Host Lane 和 1 条 Media Lane，且两侧 Lane 数量不必相同；尚未解释它们因共同实现一个端到端 Application Instance 而属于同一 Data Path。M4=62%。
- **M4 Round 7（Lane 映射）**: 正确选择“共同实现同一个 Application Instance 的端到端传输功能”作为归属同一 Data Path 的关键依据；尚未用自己的话解释。M4=66%。
- **M4 Round 8（Lane 映射）**: 正确补全：不同数量的 Host/Media lanes 仍属于同一 Data Path，因为它们服务于同一个 Application Instance，并共同完成端到端传输功能。M4=72%。
- **M4 Round 9（资源冲突诊断）**: 对两个 Data Path 的 Lane 重叠与独立性规则不清楚。保持 M4=72%，开始用资源独占场景推导“并行独立 Data Path 不共享 Lane”。
- **M4 Round 10（资源冲突）**: 正确判断释放实例 A 共享的 Lane 2 会破坏仍依赖该 Lane 的实例 B。已识别 Lane 重叠会破坏 Data Path 的独立控制。M4=75%。
- **M4 Round 11（资源冲突）**: 正确选择不重叠分配 A=Lane 1-2、B=Lane 3-4，并说明实例关闭会释放全部关联资源，因此 Lane 重叠会破坏另一实例。并行 Data Path 资源独占规则已掌握。M4=82%，进入综合验收。
- **M4 Round 12（综合验收拆解）**: 对同时涉及 Application、Instance、Data Path 和资源冲突的五问场景无法作答。保持 M4=82%，将综合题拆分为“先数模板与实例，再数 Data Path 与检查冲突”。
- **M4 Round 13（综合验收拆解）**: 正确识别模块广告 2 种 Application 定义，当前运行 4 个 Application Instance（Y1-Y4）。M4=85%。
- **M4 Round 14（综合验收拆解）**: 正确说明每个 Application Instance 对应一个 Data Path，因此 Y1-Y4 共 4 个 Data Path；其 Host Lane 组互不重叠。M4=90%，仅剩跨 Application 资源冲突判断。
- **M4 Round 15（综合验收）**: 正确判断 Y1-Y4 已占用全部 Host/Media lanes 时不能再创建 X，因为新 Data Path 将与现有 Data Path 资源重叠。M4 最终掌握度 95%，状态 mastered；进入 M5。
- **M5 Round 1（起点诊断）**: 正确判断 Host 仅写 Staged 且未 Apply 时硬件仍运行 Active 的 100G；正确解释 staging 防止逐字节写入造成部分配置立即生效；将 Staged 理解为草稿/候选配置暂存区。M5=25%。
- **M5 Round 2（Provisioning/Commissioning）**: 正确识别 Staged→Active 为 provisioning，Active→实际硬件配置为 commissioning；正确判断只完成 provisioning 时 Active 已更新但硬件不一定完成切换。M5=40%。
- **M5 Round 3（Apply 校验）**: 正确说明 Apply 后模块应先检查配置完整性与资源合法性；校验失败时 Active 应保持旧配置，避免模块按非法配置初始化硬件。尚未明确选择失败结果状态 `ConfigRejected`。M5=52%。
- **M5 Round 4（Apply 校验）**: 正确选择校验失败结果 `ConfigRejected`。M5=58%。
- **M5 Round 5（per-lane 配置）**: 正确判断 Lane 3/4 的 DataPathID 不一致，不能表示同一 Data Path；若起始 Host Lane 为 3，两条 Lane 的 DataPathID 都应为 3。M5=68%。
- **M5 Round 6（per-lane 配置）**: 正确判断 AppSelCode/DataPathID 相同的 Lane 3/4 属于同一 Data Path，且 TxEqualization 不同不一定是错误；尚未解释 SI 参数按 Lane 独立是为补偿各物理通道差异。M5=73%。
- **M5 Round 7（per-lane SI）**: 正确判断 TxEqualization 需要允许逐 Lane 调整；未完成“共同身份 vs 单 Lane 电气条件”的原因填空，继续针对性确认。M5=76%。
- **M5 Round 8（per-lane SI）**: 正确说明 AppSelCode/DataPathID 描述 Lane 的共同 Data Path 身份，而 TxEqualization 描述单条 Lane 的电气条件，可逐 Lane 调整。M5=82%，进入综合验收。
- **M5 Round 9（综合验收）**: 正确排列前三步 B→D→E（写 Staged、触发 Apply、校验），但将 ConfigSuccess 提前到 Active 更新和硬件配置之前；将写 Staged 误标为 provisioning，将 Active 更新误标为 commissioning。需重新区分：F=provisioning，A=commissioning，C=完成结果。M5 回调至 76%。
- **M5 Round 10（流程纠错）**: 正确修正后三步为 F→A→C，完整成功流程为 B→D→E→F→A→C；正确识别 F（Staged→Active）为 provisioning，A（Active→硬件）为 commissioning。M5=86%，进行失败分支验收。
- **M5 Round 11（失败分支）**: 正确判断校验失败后不 Commissioning、Active 保持旧值、报告 ConfigRejected；但误认为仍继续 Provisioning。由于 Provisioning 正是 Staged→Active，校验失败时也必须跳过。M5 回调至 83%。
- **M5 Round 12（失败分支验收）**: 正确修正：校验失败意味着候选配置不能进入 Active，因此不能 Provisioning，也不能 Commissioning；Active 保持旧值并报告 ConfigRejected。M5 最终掌握度 92%，状态 mastered；进入 M6。
- **2026-10-21 · Session Pause**: 今日课程结束。完成 M4（95%）和 M5（92%）；M6 尚未开始作答。下次从简化状态机 OFF→INITIALIZING→READY 的状态、事件、动作与转移辨析继续。
- **2026-10-22 · Session Resume**: 恢复 M6，从简化状态机基础开始。
- **M6 Round 1（起点诊断）**: 正确识别 OFF/INITIALIZING/READY 为状态，上电与初始化成功为触发转移的事件，并给出 OFF→INITIALIZING→READY 顺序。M6=20%。
- **M6 Teaching Adjustment**: 学习者再次强调，直觉答对后仍需系统解释知识点。后续采用“作答→讲解→反例/边界→迁移验证”四步法；当前状态/事件/动作题尚未作答，不计轮次与分数。
- **M6 Round 2（State/Event/Action）**: 正确分类：模块上电=Event，初始化管理接口=Action，INITIALIZING=State；正确说明 Action 是状态机响应 Event 后执行的工作。进入定义讲解与迁移验证。M6=28%。
- **M6 Round 3（迁移验证）**: 正确说明无复位 Event 时不应仅因存在关闭电路 Action 而进入 RESETTING；能解释 State 定义当前行为、Host 允许操作、功能可用性及 Event 响应，因此同一 Event 在不同 State 可产生不同结果。M6=38%。
- **M6 Round 4（稳态/瞬态）**: 正确分类 READY=Steady，RESETTING/INITIALIZING=Transient；能解释 Transient 必须有最大持续时间或超时以防卡死，而 Steady 可等待 Host 请求长期停留。M6=48%。
- **M6 Round 5（CMIS 状态迁移）**: 正确分类 ModuleLowPwr/ModuleReady 为 Steady、ModulePwrUp 为 Transient；能分别依据等待 Host 事件、内部活动完成后自动转移、以及异常 Event 说明理由。M6=58%。生成 MSM 主路径辅助图。
- **M6 Round 6（MSM 主路径）**: 正确识别 MgmtInit 为管理接口初始化阶段；对为何不能 Reset 后直接进入 ModuleReady，仅回答“中间过程可能被其他 Event 打断”。该说法属于边界条件，但未覆盖核心目的：先建立基本可管理性，在低功耗稳态暴露能力/状态，再由 Host 决定是否升功耗。M6=63%。
- **M6 Round 7（基本可管理性）**: 正确选择 ModuleLowPwr，并从管理接口已可用、Host 可读取/配置、无需立即启动高功耗硬件三个角度解释。已理解基本可管理性与完全运行态分离。M6=70%。
- **M6 Round 8（Reset/Fault）**: 正确判断 Host 复位请求进入 Resetting，严重内部安全故障进入 ModuleFault；能说明继续运行可能造成设备损坏或安全风险。M6=78%。
- **M6 Round 9（Fault 恢复）**: 正确判断故障条件表面消失后不应从 ModuleFault 自行返回 ModuleReady；需复位或重新上电以清理资源、重新初始化并建立可信状态。M6=85%，进入综合验收。
- **M6 Round 10（综合验收未通过）**: 使用过程编号作答而未写出 CMIS 状态路径，顺序与 Steady/Transient 分类也未能对应实际状态；暴露出“过程描述→状态名”的映射尚不稳定。M6 回调至 82%，进行针对性复习后再次验收。
- **M6 Round 11（降功耗路径复测）**: 正确写出 ModuleReady → ModulePwrDn → ModuleLowPwr，并正确分类 ModulePwrDn 为 Transient、ModuleLowPwr 为 Steady；过程描述到状态名的映射已恢复稳定。M6=88%，进行最终综合验证。
- **M6 Round 12（上电路径综合验证）**: 正确写出 Resetting → Reset → MgmtInit → ModuleLowPwr → ModulePwrUp → ModuleReady；正确分类 Resetting/MgmtInit/ModulePwrUp 为 Transient，Reset/ModuleLowPwr/ModuleReady 为 Steady。路径中的 Rest 属拼写笔误，分类中已正确写为 Reset。M6=94%，仅剩高优先级事件边界验收。
- **M6 Round 13（高优先级事件边界）**: 正确判断 ModulePwrUp 期间收到 Reset 请求应中断升功耗并进入 Resetting。理由需精确为 Reset 是规范定义的高优先级转移条件，而非所有 Transient 都必须响应所有 Event。M6=96%，状态 mastered；进入 M7。
- **M7 Round 1（起点诊断）**: 正确指出通过 Page/Bank 机制扩展 8-bit 地址空间，并说明 Upper Memory 地址的解释需要选择上下文。需精确化：0x00–0x7F Lower Memory 不受 Page 选择影响；0x80–0xFF Upper Memory 由 Page 选择，只有 Banked Page 还需要 Bank 选择。M7=20%。
- **M7 Round 2（Lower/Upper 地址窗口）**: 正确判断 0x20 属于 Lower Memory 且不随 Page 改变，0x90 属于 Upper Memory 且 FW 必须结合 Page Select 解释。主动追问 Banked Page 的定义与规范归属，进入 Page/Bank 两级寻址。M7=32%。
- **M7 Round 3（Banked Page 实例）**: 正确说明相同 Page/地址在不同 Bank 下访问的是不同页面实例；对 16-Lane lane-banked Page，Bank 0 对应 Lane 1–8、Bank 1 对应 Lane 9–16。M7=45%。
- **M7 Round 4（Bank 支持边界）**: 正确判断 8-Lane 模块不能把 Page 10h/Bank 1 当作 Lane 9–16 的正常访问，并指出 Banked Page 不等于所有 Bank 实例都存在。M7=55%。
- **M7 Round 5（PageMapping 译码）**: 正确判断 0x30 属于 Lower Memory，不使用 BankSelect/PageSelect；0x90 属于 Upper Memory，Page 10h 为 Banked 时需同时结合 PageSelect 与 BankSelect，Bank 1 对应 Lane 9–16。M7=65%。
- **M7 Round 6（PageMapping 原子切换）**: 正确选择在一次从 Byte 126 开始的 WRITE 中写入 [BankSelect, PageSelect]，避免 Bank/Page 半更新组合。M7=73%。
- **M7 Round 7（Page Remapping 时序）**: 正确判断映射未完成时不能返回旧 Page 的 stale data；能区分 PageMapping 写入完成与内部 Page Remapping 完成，新 Upper Memory 数据需等待模块准备好后才能访问。M7=82%，进入综合验收。
- **M7 Round 8（综合验收）**: 正确排序 C→B→A：一次 WRITE 提交 [BankSelect=01h, PageSelect=10h]，等待/TEST 确认模块准备好，再读取 0x90；正确说明切换中不能返回旧 Page stale data，应拒绝可能访问 stale data 的 ACCESS。M7=92%，状态 mastered；进入 M8。
- **M8 Round 1（数据性质诊断）**: 正确分类 A 厂商/料号/序列号为身份信息，B Application/速率/Lane 组合为能力广告，C 温度/电压告警值为 Threshold。M8=18%，进入具体 Page 用途映射。
- **M8 Round 2（Page 00h–02h 映射）**: 正确将厂商/料号验证映射到 Page 00h，将 Application/Lane Assignment 能力映射到 Page 01h，将温度 High Alarm Threshold 映射到 Page 02h。M8=35%。
- **M8 Round 3（Page 03h–05h 映射）**: 正确将 Host 资产标签写入映射到 Page 03h User EEPROM，将可调激光器能力/Channel 范围映射到 Page 04h，将 Form-Factor-specific 管理信号映射到 Page 05h。M8=52%。
- **2026-10-22 · Session Pause**: 学习者结束今日课程。已掌握 M1–M7；M8 当前 52%。下次从 Optional Page 访问顺序 B→C→A 及“不先检查支持可能映射回 Page 00h”的原因继续；该题尚未作答，不增加提问轮次。
- **2026-10-23 · Session Resume**: 恢复 M8（52%），从 Optional Page 03h 的发现、选择、等待 Remapping、访问顺序继续。已确认 `review-notes.md` 覆盖上一轮内容。
- **M8 Round 4（Optional Page 访问流程）**: 正确排序 B→C→A：先从 Page 01h 确认 Page 03h 支持，再选择 Page 03h 并等待 Remapping，最后写 User EEPROM；正确说明不能跳过支持检查和映射等待。需精确化：Remapping 未完成时访问可能仍指向旧 Page，模块应拒绝可能造成 stale/wrong-page access 的操作，而不只是笼统地“写不成功”。M8=65%。
- **M8 Round 5（页面读写属性）**: 正确判断 Page 00h Vendor Name 与 Page 02h Threshold 为只读，Host 不可修改；正确指出 Page 03h 写入前需确认支持并完成页面选择/Remapping。需精确化：题设已确认支持时，Page 03h 本身是 Host-writable，完成映射并满足 EEPROM 写时序后可以写入。M8=78%，进入综合验收。
- **M8 Round 6（综合验收部分通过）**: 正确回答 Vendor Name 位于 Page 00h，并正确描述 Page 03h 的支持检查、选择/Remapping 等待及 EEPROM 写周期/范围要求；但漏答 Application 支持应查 Page 01h、温度 High Alarm Threshold 应查 Page 02h。M8=82%，针对遗漏项补测。
- **M8 Round 7（遗漏项补测）**: 正确回答 Application Advertising 位于 Page 01h，温度 High Alarm Threshold 位于 Page 02h。已稳定掌握 Page 00h–05h 的用途、支持属性、RO/RW 边界及 Optional Page 访问流程。M8=92%，状态 mastered；进入 M9。
- **M9 Round 1（三层模型起点诊断）**: 正确区分 A 为 Data Path 配置，B 为 Data Path 整体状态，C 为单 Lane Tx 控制/输出状态。补充关键耦合边界：在 DPActivated 设置某一 Media Lane 的 Host OutputDisableTx/Force Squelch，会使整个 Data Path 转向 DPInitialized；外部 LOS 引发的自动 squelch 则可能不改变 DPActivated。M9=18%。
- **M9 Round 2（Lane 控制与 DPSM 耦合暴露）**: 对 Host OutputDisableTx 和 LOS 自动 squelch 是否导致 DPActivated 退出均回答“不知道”。确认当前缺口不在三层分类，而在触发源与 DPSM 转移的因果关系。重新讲解：Host Disable/Force Squelch 形成 deactivation 条件，整个 Data Path 经 DPTxTurnOff 进入 DPInitialized；LOS 自动 squelch 是运行中的输出保护行为，不必退出 DPActivated。M9 保持 18%，立即复测。
- **教学规则调整（2026-10-23）**: 学习者指出未解释的术语/缩略词会妨碍理解。后续所有新术语首次出现时，按“英文全称 → 中文含义 → 用途/所属层次 → 必要的相近概念辨析”介绍；本轮补齐 M9 已出现术语后再复测，上一道未作答复测不计轮次。
- **M9 Round 3（Lane/DPSM 耦合复测）**: 两题均正确：Host 设置 OutputDisableTx 会使整个 Data Path 离开 DPActivated 并最终进入 DPInitialized；外部 LOS 导致的自动 squelch 不一定改变 DPSM，Data Path 可以继续处于 DPActivated。已掌握“运行意图变化”与“运行中保护行为”的区别。M9=30%，进入 DPSM 主路径。
- **M9 Round 4（DPSM 状态类型）**: 正确分类稳态为 DPActivated、DPDeactivated、DPInitialized（A/C/E），暂态为 DPInit、DPTxTurnOn（B/D）。题目要求的正常激活顺序尚未回答，需单独补测。M9=36%。
- **M9 Round 5（DPSM 激活顺序补测）**: 正确排列 C→B→E→D→A，即 DPDeactivated → DPInit → DPInitialized → DPTxTurnOn → DPActivated。已掌握正常激活主路径及稳态/暂态分类。M9=45%，进入正常停用路径。
- **M9 Round 6（DPSM 停用顺序）**: 正确排列 D→E→C→B→A，即 DPActivated → DPTxTurnOff → DPInitialized → DPDeinit → DPDeactivated。已理解先关闭对外 Tx 输出，再撤销内部硬件资源。M9=55%，进入暂态完成与超时监控。
- **M9 Round 7（暂态超时处理）**: 正确选择 C：DPInit 停留超过模块广告的最大持续时间时，不能无限等待或视为成功；Firmware 应判定初始化超时，记录状态、时长及相关 Lane 信息，并进入错误恢复。M9=63%，进入 Page 11h 的 DataPathState 状态观测。
- **M9 Round 8（Page 11h 状态解码）**: 正确将四条 Host Lane 的编码 4h 解码为 DPActivated，并判断四条 Lane 状态一致、整个 Data Path 处于正常激活状态。已掌握 Page 11h 的基本状态编码与多 Lane 一致性判断。M9=70%，进入不一致状态诊断。
- **Session Pause（上午）**: 学习者暂停课程，下午继续。M9 当前 70%；已讲解 Snapshot、Atomic Read、Race Condition 与 Timestamp。恢复时先回答尚未计轮次的场景题：同一 Data Path 的 Lane 状态不一致时，应保存快照和时间戳、短暂等待并完整重读，而不是任选一条 Lane 或立即重配置。
- **Session Resume（下午）**: 恢复 M9（70%），从 Page 11h 的多 Lane 状态不一致诊断题继续；该题此前尚未作答，仍不增加累计提问轮次。
- **M9 Round 9（多 Lane 状态不一致诊断）**: 正确选择 C：先保存快照和时间戳，短暂等待后完整重读所有关联 Lane，以区分读取期间发生状态转移造成的竞态与持续的真实异常；不能任选一条 Lane，也不能立即重配置。M9=78%，进入 Page 11h Lane Output Status。
- **M9 Round 10（DPSM 与 Output Status 联合判断暴露）**: 对 `DPActivated + OutputStatusTx3=0 + Host 未 Disable + 相关输入 LOS` 场景回答“不知道”。确认缺口：尚未稳定区分生命周期状态、实际输出状态与导致输出无效的原因。重新讲解三层诊断：DPSM 仍可为 DPActivated；OutputStatusTx3=0 仅表示 Lane 3 当前未输出有效信号；在无 Host Disable 且存在 LOS 时，最可能是 LOS 触发自动 squelch。M9 保持 78%，立即复测。
- **M9 Round 11（DPSM 与 Output Status 联合判断复测）**: 正确选择 B：Data Path 可以继续处于 DPActivated，而 Lane 3 因相关输入 LOS 被自动 squelch，故 OutputStatusTx3=0。已掌握生命周期状态、实际输出状态与原因条件的三层诊断。M9=84%，达到掌握门槛；继续完成 Page 10h/11h 控制—状态闭环后再做综合验收。
- **M9 Round 12（Page 10h/11h 闭环验收）**: 正确选择 B：对使用 Host Lane 1–4 的 Data Path，应将关联 Lane 的 DPDeinit 请求全部设为 1，再轮询 Page 11h 并确认所有关联 Lane 最终一致报告 DPDeactivated。正确排除“只写一条 Lane”和“直接写只读 Page 11h 状态”的错误做法。M9=92%，状态 mastered；进入 M10。
- **M10 Round 1（诊断信息分类）**: 正确分类当前温度为 Sample、High Alarm Threshold 为 Threshold、High Alarm Flag 为 Flag、统计区间内 BER 最大值为 Statistic。已掌握事实值、判断标准、判断结果和区间汇总的基本区别。M10=18%，进入 VDM Instance/Descriptor 发现机制。
- **Session Pause（今日结束）**: 学习者结束今日课程。M1–M9 已掌握，M10 当前 18%；已讲解 VDM 全称与用途、Sample/Threshold/Flag/Statistic 分类，以及 VDM Instance/Descriptor 的模块自定义映射。下次先回答尚未计轮次的 BER 读取题：不能固定读取 Instance 1，必须先从 Descriptor Array 定位 BER 对应的 Active Instance。
