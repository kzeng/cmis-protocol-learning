# OIF-CMIS 5.3 学习与 Firmware 开发复习手册

> 教材：OIF-CMIS-05.3（Rev 5.3）  
> 学习目标：面向光模块 Firmware 开发，建立从协议语义、管理访问、配置事务、状态机到 Memory Map 的完整心智模型。  
> 文档性质：持续更新的长期复习资料。`session.md` 保存学习轨迹，本文件保存整理后的知识体系。  
> 当前覆盖：M1–M7 已掌握；M8 学习至 Page 00h–05h 的主要用途。

---

## 0. 如何使用本手册

建议按三种方式复习：

1. **快速复习**：只看每章的“一句话模型”“关键流程”和“常见误区”。
2. **工程复习**：重点看“Firmware 实现提示”和伪代码。
3. **掌握检查**：遮住答案，完成每章末尾的自测题。

后续维护规则：

- 每轮学习与 `session.md`、`roadmap.html` 同步检查和维护本文件。
- 每完成一个知识点，补充其定义、工程原因、边界、例子和 Firmware 实现提示。
- 每出现一次有价值的误解，加入“常见误区”。
- 每完成一个模块，补充综合流程和自测题；每组自测题下方必须提供可折叠的参考答案与解析。折叠块内部统一使用自包含 HTML（列表、粗体、行内代码和代码块均用 HTML 标签），不在 HTML 容器内混用 Markdown。
- 规范原文与教学抽象冲突时，以 OIF-CMIS 5.3 规范为准，并记录纠正原因。

---

# M1 光模块与 CMIS 全景

## 1.1 CMIS 管理的对象

光模块同时存在三类接口/通道：

```text
交换机管理控制器
        │
        │ CMIS 管理接口：状态、配置、告警、诊断、复位
        ▼
      光模块
        ▲                         ▲
        │ Host 高速接口           │ Media 高速接口
        │                         │
交换机 ASIC                  光纤、铜缆或远端设备
```

- **CMIS 管理接口**：低速管理平面，用于读取身份、温度、告警、状态、FW 版本，以及执行配置和复位等操作。
- **Host 高速接口**：模块与交换机 ASIC 之间的高速电气数据接口。
- **Media 高速接口**：模块与光纤、铜缆或远端设备之间的高速媒体接口。

## 1.2 为什么管理通道必须独立

高速业务不通时，Host 仍需通过管理接口判断：

- 模块是否上电；
- 管理接口是否初始化；
- 温度、电压是否异常；
- Data Path 是否配置或激活；
- 是否需要 Reset；
- 故障位于 Host 电气侧、模块内部还是 Media 侧。

因此：

```text
高速业务失败 ≠ 模块整体不可管理
```

如果 Host 已经能读到 Vendor、FW Version 和温度，则至少说明管理通道已经工作，不能再断言“模块完全没有响应”。

## 1.3 抽象启动依赖

早期学习阶段建立的抽象顺序是：

```text
模块上电
→ 管理接口可用
→ Host 读取能力
→ Host 写入配置
→ 高速电路具备工作条件
→ 建立高速 Data Path
```

后续 M5、M6 会用 Control Set、MSM 和 DPSM 对该过程进行精确定义。

## 1.4 Firmware 实现提示

- 管理任务不能依赖高速数据链路已经建立。
- 即使 DSP、SerDes 或 Laser 初始化失败，也应尽可能保留故障报告能力。
- 调试时应分别记录 Management、Host-side 和 Media-side 状态，避免把三者混成“模块通/不通”。

## 1.5 常见误区

- **误区**：网络流量不通，说明模块完全损坏。  
  **纠正**：管理通道可能正常，问题可能只存在于某个 Data Path 或某一侧高速接口。
- **误区**：Host 高速接口就是 CMIS 管理接口。  
  **纠正**：二者服务于不同平面，速率、协议和职责均不同。

## 1.6 自测

1. Host 能读取温度，但链路不通，能否断言模块未进入可管理状态？
2. 为什么管理接口应先于业务 Data Path 可用？
3. 交换机 ASIC、管理控制器、光纤分别连接哪一类接口？

<details>
<summary><strong>参考答案与解析</strong></summary>

<ol>
<li>
<p><strong>不能。</strong> Host 能读取温度，已经证明 CMIS 管理通道至少具备相应的访问能力。链路不通可能来自 Host-side SerDes、Data Path 配置、模块内部高速电路、Media 侧光路或远端设备，不能据此否定模块的可管理性。</p>
</li>
<li>
<p>管理接口必须先可用，Host 才能读取模块身份和能力、诊断启动失败、写入配置并决定是否启动高功耗电路。若管理能力依赖业务 Data Path，业务失败时就失去了诊断和恢复手段。</p>
</li>
<li>
<p><strong>交换机 ASIC → Host 高速接口；管理控制器 → CMIS 管理接口；光纤/铜缆/远端 → Media 高速接口。</strong> 三者属于不同功能平面，应分别观察和调试。</p>
</li>
</ol>

</details>

---

# M2 术语、数据类型与数据操作

## 2.1 十六进制与固定宽度表示

一个十六进制数字对应 4 bit：

```text
0x05 = 0000 0101
0x50 = 0101 0000
```

读取寄存器时应保持规范给出的宽度。`0x05` 虽然也可写成二进制 `101`，但按 Byte 分析时应写为 `0000 0101`，避免高低位混淆。

Bit 编号通常从 0 开始：

```text
bit 7 6 5 4 3 2 1 0
```

## 2.2 单 bit 操作

### 测试 bit

```c
bool set = (status & (1U << bit_index)) != 0U;
```

例如 bit 2：

```text
1U << 2 = 0000 0100 = 0x04
```

### 设置 bit

```c
control |= (1U << bit_index);
```

OR 的作用是保留其他位，并把目标位置 1。

### 清除 bit

```c
control &= ~(1U << bit_index);
```

AND 配合反掩码保留其他位，并把目标位清 0。

## 2.3 连续字段提取

对于字段 `bits 4:2`：

```text
宽度 = 4 - 2 + 1 = 3
最低位 = 2
mask = ((1U << 3) - 1U) << 2
     = 0001 1100
     = 0x1C
```

提取公式：

```c
field = (value & mask) >> low_bit;
```

示例：

```text
value = 1011 0100
mask  = 0001 1100
AND   = 0001 0100
>> 2  = 0000 0101
```

关键理解：

- Mask 中的 1 表示“允许原数据通过”，不是强制将结果置 1。
- AND 后字段仍在原 bit 位置；右移才把字段对齐到 bit 0。

## 2.4 枚举值与保留值

- **Defined value**：规范已经赋予明确语义。
- **Reserved**：保留给规范未来使用，厂商不能擅自赋义。
- **Vendor Specific**：规范明确允许厂商定义。

```text
Reserved ≠ Vendor Specific
```

私自使用 Reserved 会导致不同版本 Host 与 Module 对相同编码作出不同解释。

## 2.5 规范关键字

- **shall**：强制要求；违反后不能宣称符合该项规范。
- **should**：强烈建议；有充分工程理由时可以偏离。
- **may**：允许的自由选择。
- **optional**：可以不实现；但一旦广告支持，就必须遵守该功能的寄存器、状态和流程定义。

## 2.6 常见误区

- bit 2 的掩码误写为 `0x02`。正确值是 `1 << 2 = 0x04`。
- 把字段最高位当成右移量。正确右移量是字段最低位编号。
- 认为 `value & mask` 一定等于 mask。实际结果由 value 原始位决定。
- 把 Reserved 当成厂商可自定义空间。

## 2.7 自测

1. `0x05` 的 8-bit 二进制是什么？哪些 bit 为 1？
2. 如何设置 bit 5、清除 bit 3？
3. 写出提取 `bits 6:4` 的 mask 和表达式。
4. Optional 功能未实现是否违规？广告支持后不符合其流程是否违规？

<details>
<summary><strong>参考答案与解析</strong></summary>

<ol>
<li>
<p><code>0x05 = 0000 0101</code>，bit 0 和 bit 2 为 1。一个十六进制位对应 4 bit，因此分析 Byte 时应保留前导 0。</p>
</li>
<li>
<p>设置 bit 5：<code>value |= (1U &lt;&lt; 5)</code>；清除 bit 3：<code>value &amp;= ~(1U &lt;&lt; 3)</code>。OR 用于保留其他位并置位，AND 配合反掩码用于清位。</p>
</li>
<li>
<p>字段宽度为 <code>6 - 4 + 1 = 3</code>，最低位是 4：</p>
<pre><code class="language-c">mask  = ((1U &lt;&lt; 3) - 1U) &lt;&lt; 4;  // 0x70
field = (value &amp; mask) &gt;&gt; 4;</code></pre>
<p>掩码先产生 3 个连续的 1，再左移到 bits 6:4；提取后右移最低位编号 4。</p>
</li>
<li>
<p><strong>Optional 功能未实现通常不违规。</strong>但模块一旦广告支持该功能，就必须遵守其寄存器、状态、时序和流程要求；广告支持却不按规范实现会造成互操作问题，也不能视为符合该功能要求。</p>
</li>
</ol>

</details>
---

# M3 管理接口分层：MSL 与 RAL

## 3.1 MSL 与 RAL

### MSL：Management Signal Layer

描述专用管理信号，例如复位、低功耗、中断等离散硬件信号。

### RAL：Register Access Layer

描述通过管理通信接口对寄存器执行的抽象操作：

```text
READ / WRITE / TEST
```

## 3.2 ASSERTED 是功能语义

`ASSERTED` 表示某项管理功能正在被请求或生效，不等同于固定高电平。

```text
低有效信号：物理低电平可能表示 ASSERTED
高有效信号：物理高电平可能表示 ASSERTED
```

这样可以将 CMIS 的功能语义与不同封装的电气极性解耦。

## 3.3 RAL TEST() 的正确含义

这是学习中曾重点纠正的定义：

> `TEST()` 检查模块是否准备好接受下一次 RAL READ/WRITE。

它**不是**检查某个寄存器 bit。

典型 Host 流程：

```text
TEST
├─ ready → READ/WRITE
└─ not ready → 等待并重试，直到超时
```

## 3.4 Access Hold-Off 与永久错误

- **临时忙/内部处理**：属于 Access Hold-Off，等待后可能恢复。
- **写只读寄存器、非法访问方式**：等待不能解决，是访问本身不合法。

Host 必须设置最大重试次数或超时，防止异常模块永久阻塞管理线程。

## 3.5 多字节一致性

动态 16-bit 数据若拆成两次 1-byte READ：

```text
先读 High Byte
数据发生更新
再读 Low Byte
```

可能拼出一个从未真实存在过的混合值。若规范保证 size-matched 2-byte READ 的一致性，应一次读取完整字段。

## 3.6 典型处理中断流程

```text
检测 MSL Interrupt ASSERTED
→ RAL TEST，等待访问就绪
→ 用一次合适长度的 READ 获取状态/计数
→ 解析 Flag 和数据
→ 按规范处理清除、Mask 或恢复动作
```

## 3.7 Firmware 实现提示

- 将 MSL 电气极性转换成统一的逻辑 ASSERTED/DEASSERTED。
- RAL 层统一实现 busy、retry、timeout 和错误上报。
- 为多字节动态字段提供一致性快照或原子读取机制。

## 3.8 自测

1. 为什么 ASSERTED 不能定义成固定高电平？
2. TEST=false 时 Host 应做什么？
3. 为什么两次 1-byte READ 可能破坏 16-bit 动态字段的一致性？

<details>
<summary><strong>参考答案与解析</strong></summary>

<ol>
<li>
<p>因为不同封装或信号可能是高有效或低有效。<code>ASSERTED</code> 描述的是“功能正在被请求/生效”的逻辑语义，而不是固定电压；这样 MSL 才能与具体电气极性解耦。</p>
</li>
<li>
<p>Host 应等待后重新 <code>TEST()</code>，在模块准备好后再执行 READ/WRITE，并设置最大重试次数或超时。不能在 <code>TEST=false</code> 时强制访问，也不能无限等待而不向上层报告故障。</p>
</li>
<li>
<p>动态字段可能在两次读取之间更新。例如先读到旧 High Byte，随后计数器变化，再读到新 Low Byte，最终拼出一个从未真实存在的混合值。若规范支持 size-matched 2-byte READ，应一次读取完整字段以获得一致快照。</p>
</li>
</ol>

</details>
---

# M4 Application、Application Instance 与 Data Path

## 4.1 三个核心概念

### Application

模块广告的一种能力模板，例如某种速率、调制/编码以及 Host/Media Lane 需求。

```text
Application = “模块能够怎样工作”
```

### Application Instance

Application 被实际部署到具体 Lane 和资源后形成的运行实例。

```text
Application Instance = Application + 具体资源绑定 + 独立配置/状态
```

同一个 Application 可创建多个 Instance，每个实例可处于不同状态。

### Data Path

一个 Application Instance 使用的完整端到端资源集合，包括：

- Host Lanes；
- Media Lanes；
- 内部 DSP/PCS/FEC/SerDes 等资源；
- 与实例相关的配置和运行状态。

## 4.2 Host/Media Lane 数量可以不同

例如一个 Data Path 可以使用：

```text
4 条 Host Lane + 1 条 Media Lane
```

两侧 Lane 数不同并不妨碍它们属于同一 Data Path；关键是它们共同服务于同一个 Application Instance，完成端到端传输。

## 4.3 Data Path 的资源独立性

并行独立 Data Path 不应共享同一 Lane。否则：

- 关闭实例 A 时释放共享 Lane；
- 实例 B 仍依赖该 Lane；
- B 的独立控制被破坏。

因此创建新实例前必须检查 Host Lane、Media Lane 和内部资源冲突。

## 4.4 数量关系

```text
一种 Application 定义
    可以产生多个 Application Instance

一个 Application Instance
    对应一个 Data Path
```

## 4.5 常见误区

- 把 Application 当成当前已经运行的链路。Application 只是能力模板。
- 认为同一 Application 的多个实例必须具有相同状态。
- 认为 Host Lane 数必须等于 Media Lane 数。
- 只检查 Host Lane，不检查 Media Lane 和内部资源重叠。

## 4.6 自测

1. “模块支持 100G/2 Host Lanes”与“100G 正运行在 Lane 3–4”分别是什么？
2. 一个实例为何必须绑定具体资源并拥有独立状态？
3. 两个独立 Data Path 能否共享 Lane？为什么？

<details>
<summary><strong>参考答案与解析</strong></summary>

<ol>
<li>
<p>“模块支持 100G/2 Host Lanes”是 <strong>Application</strong>，描述能力模板；“100G 正运行在 Lane 3–4”是一个 <strong>Application Instance</strong>，已经绑定具体 Lane 和运行状态。</p>
</li>
<li>
<p>同一种 Application 可以同时部署多次。只有绑定具体 Host/Media Lane、内部资源、配置和状态后，模块才能独立初始化、监控、关闭和回收每个实例。</p>
</li>
<li>
<p>两个并行且要求独立控制的 Data Path <strong>不应共享 Lane</strong>。否则关闭或重配置其中一个 Data Path 时，会修改或释放另一个仍在使用的资源，破坏实例独立性。资源检查还应覆盖 Media Lane 和内部资源，而不只是 Host Lane。</p>
</li>
</ol>

</details>
---

# M5 Control Set 与配置事务

## 5.1 Staged 与 Active Control Set

- **Staged Control Set**：Host 正在编写的候选配置，类似草稿。
- **Active Control Set**：模块已经接受的有效配置。

Host 按 Byte 写配置时，不能让每个 Byte 立即影响硬件，否则会暴露半配置状态。因此先写 Staged，完整后再 Apply。

## 5.2 Provisioning 与 Commissioning

```text
Provisioning：Staged → Active
Commissioning：Active → 实际硬件
```

二者不能混淆：

- 写 Staged 不是 Provisioning；
- Active 更新是 Provisioning；
- 把 Active 配置施加到硬件是 Commissioning。

## 5.3 成功流程

```text
Host 写 Staged
→ Host 触发 Apply
→ 模块校验完整性、能力、Lane 和资源合法性
→ Provisioning：Staged 复制/提交到 Active
→ Commissioning：Active 应用到硬件
→ ConfigSuccess
```

`ConfigSuccess` 不能在 Active 更新和硬件应用之前提前报告。

## 5.4 失败流程

```text
Host 写 Staged
→ Apply
→ 校验失败
→ 不 Provisioning
→ 不 Commissioning
→ Active 保持旧值
→ ConfigRejected
```

校验失败时，候选配置不能进入 Active。因为 Provisioning 本身就是 Staged→Active，所以不能说“失败后仍 Provisioning，但不 Commissioning”。

## 5.5 Data Path 身份字段与逐 Lane SI 参数

同一 Data Path 的 Lane 应保持身份一致，例如：

- `AppSelCode`；
- `DataPathID`。

但 Signal Integrity 参数可以逐 Lane 不同，例如 `TxEqualization`，因为不同 PCB trace、连接器、SerDes 和通道损耗需要独立补偿。

```text
共同身份字段：描述“属于哪个 Data Path”
逐 Lane SI 字段：描述“该物理 Lane 如何调优”
```

## 5.6 Firmware 实现提示

- Staged 与 Active 使用不同存储区，避免半更新。
- Apply 时做一次完整事务校验。
- 资源校验应覆盖 Lane 重叠、Application 支持、字段一致性和参数范围。
- 失败路径不得修改 Active 或硬件。
- 状态/结果更新顺序应保证 Host 不会看到虚假的成功。

## 5.7 自测

1. Host 只写 Staged 未 Apply 时，硬件按哪份配置运行？
2. Provisioning 与 Commissioning 分别是什么？
3. Apply 校验失败后 Active、硬件和结果状态分别如何处理？
4. 为什么同一 Data Path 的 TxEqualization 可以逐 Lane 不同？

<details>
<summary><strong>参考答案与解析</strong></summary>

<ol>
<li>
<p>硬件仍按原来的 <strong>Active Control Set</strong> 运行。Staged 只是候选配置，未 Apply 前不能影响当前有效配置或硬件。</p>
</li>
<li>
<p><strong>Provisioning：Staged → Active；Commissioning：Active → 实际硬件。</strong>写入 Staged 本身不是 Provisioning，硬件配置动作也不能与 Active 更新混为一谈。</p>
</li>
<li>
<p>校验失败时：Active 保持旧值；不执行 Provisioning；不执行 Commissioning；硬件继续使用旧配置；向 Host 报告 <code>ConfigRejected</code>。失败候选不能进入 Active。</p>
</li>
<li>
<p><code>AppSelCode</code>、<code>DataPathID</code> 等共同身份字段描述 Lane 属于哪个 Data Path，应保持一致；<code>TxEqualization</code> 描述单条物理 Lane 的电气补偿。各 Lane 的 trace、连接器和损耗不同，因此允许逐 Lane 调整。</p>
</li>
</ol>

</details>
---

# M6 Module State Machine（MSM）

## 6.1 State、Event、Action、Transition

- **State**：模块当前所处模式，定义可用功能、Host 可执行操作及事件响应。
- **Event**：可能触发状态机评估的事件或条件变化。
- **Action**：状态机响应 Event 执行的工作。
- **Transition**：满足条件时从当前 State 转移到下一 State。

Event 不一定导致 Transition；结果取决于当前 State、条件和优先级。

## 6.2 Steady 与 Transient State

### Steady State

- 可以长期等待 Host 或其他事件；
- 没有“内部活动必须在固定时间自动完成”的含义；
- 仍可能被 Reset、Fault 等事件打断。

### Transient State

- 正在执行有目标的内部活动；
- 正常完成后应自动退出；
- 应有最大持续时间或故障恢复边界；
- 不代表必须响应所有普通 Event。

## 6.3 Paged Memory Module 的主要状态

| State | 类型 | 核心含义 |
|---|---|---|
| Resetting | Transient | 关闭、清理并建立复位条件 |
| Reset | Steady | 保持复位，等待退出条件 |
| MgmtInit | Transient | 初始化 Memory Map 和管理通信接口 |
| ModuleLowPwr | Steady | 管理接口可用，模块保持低功耗 |
| ModulePwrUp | Transient | 启动高功耗电子电路 |
| ModuleReady | Steady | 模块完全可操作，可管理 Data Path |
| ModulePwrDn | Transient | 关闭高功耗电路，返回低功耗 |
| ModuleFault | Steady | 严重故障保护状态 |

## 6.4 正常上电路径

```text
Resetting
→ Reset
→ MgmtInit
→ ModuleLowPwr
→ ModulePwrUp
→ ModuleReady
```

为什么不能 `Reset → ModuleReady`：

1. `MgmtInit` 先初始化 Memory Map 默认值和管理接口；此时模块可能忽略 MCI 访问。
2. `ModuleLowPwr` 建立“基本可管理性”，Host 可在低功耗下读取身份、能力并配置模块。
3. Host 或控制条件再决定是否值得启动高功耗电路。

```text
基本可管理 ≠ 所有高速功能都可用
```

## 6.5 降功耗路径

```text
ModuleReady
→ ModulePwrDn
→ ModuleLowPwr
```

在 `ModulePwrDn` 中，即使升功耗条件重新出现，也应先完成降功耗并进入 `ModuleLowPwr`，再重新判断：

```text
ModuleReady
→ ModulePwrDn
→ ModuleLowPwr
→ ModulePwrUp
→ ModuleReady
```

不应在硬件关闭未完成时直接反转回 Ready。

## 6.6 Reset 与 ModuleFault

- Host 请求复位：通常进入 `Resetting`。
- 检测到可能造成物理损坏或安全风险的严重故障：进入 `ModuleFault`。

普通 Warning 不一定进入 ModuleFault，例如温度略超 Warning 阈值可只设置 Flag、产生 Interrupt 或降额运行。

`ModuleFault` 表示内部状态可能已经不可信。即使外部故障条件恢复，也不应直接返回 `ModuleReady`；通常需 Reset 或重新上电，重新初始化并建立可信状态。

## 6.7 高优先级事件

`ModulePwrUp` 收到 Reset 请求时，应中断升功耗并进入 `Resetting`。原因不是“Transient 必须响应所有 Event”，而是 Reset 是规范定义的高优先级转移条件，可从多数状态触发复位路径。

## 6.8 Firmware 实现提示

- 每个 Transient State 设计完成条件、最大时限和错误路径。
- 状态入口统一更新 `ModuleState`，并按规范处理 Flag。
- 明确普通事件、Reset 和 Fault 的优先级。
- `ModuleReady` 只表示模块整体完全可操作，不代表每个 Data Path 都已激活；Data Path 还由 DPSM 管理。

## 6.9 自测

1. 写出完整上电主路径，并分类 Steady/Transient。
2. 为什么 ModuleLowPwr 对系统功耗管理很重要？
3. ModuleFault 条件消失后为何不能直接返回 Ready？
4. ModulePwrDn 途中重新收到升功耗条件，应如何处理？

<details>
<summary><strong>参考答案与解析</strong></summary>

<ol>
<li>
<p>主路径：</p>
<pre><code class="language-text">Resetting → Reset → MgmtInit → ModuleLowPwr → ModulePwrUp → ModuleReady</code></pre>
<ul>
<li>Transient：<code>Resetting</code>、<code>MgmtInit</code>、<code>ModulePwrUp</code></li>
<li>Steady：<code>Reset</code>、<code>ModuleLowPwr</code>、<code>ModuleReady</code></li>
</ul>
<p>Transient 正在执行有完成目标的内部活动；Steady 可等待外部条件长期停留。</p>
</li>
<li>
<p><code>ModuleLowPwr</code> 在管理接口完全可用的同时保持低功耗。Host 可以先读取身份和能力、准备配置，再决定是否启动高功耗电路，避免模块插入后无条件消耗最大功率。</p>
</li>
<li>
<p><code>ModuleFault</code> 表示内部配置、硬件和软件状态可能已经不可信。外部指标恢复并不能证明次生错误已清除，因此通常需要 Reset 或重新上电，重新初始化并建立可信状态。</p>
</li>
<li>
<p>应继续完成 <code>ModulePwrDn</code>，进入 <code>ModuleLowPwr</code>，然后重新检查升功耗条件；如果条件仍成立，再进入 <code>ModulePwrUp</code>。不能在关闭流程未完成时直接反向跳回 <code>ModuleReady</code>。</p>
</li>
</ol>

</details>
---

# M7 Lower Memory、Page 与 Banked Memory

## 7.1 8-bit 地址空间如何扩展

Host 可直接发出的 Byte Address 为 `0x00–0xFF`，共 256 Byte：

```text
0x00–0x7F：Lower Memory，128 Byte
0x80–0xFF：Upper Memory，128 Byte 窗口
```

## 7.2 Lower Memory

Lower Memory 静态解释：

```text
相同 Byte Address → 始终表示相同寄存器
```

PageSelect/BankSelect 的改变不改变 Lower Memory 的地址含义。这里主要放需要立即访问的状态、Flags、Monitors 和 PageMapping 字段。

## 7.3 Upper Memory

Upper Memory 动态解释：

```text
Unbanked Page：Page + Byte Address
Banked Page：Page + Bank + Byte Address
```

同一个 `0x90` 在不同 Page，或在 Banked Page 的不同 Bank 中，可以指向不同数据。

## 7.4 Page 与 Bank 的关系

```text
Page = 选择哪一类数据
Bank = 选择该类数据的哪个实例
```

Bank 是部分 Page 的第二级选择，并非所有 Page 都使用 Bank。

- 非 Banked Page：BankSelect 被忽略。
- Banked Page：还需确认所选 Bank 是否由当前模块实现。
- Banked 不一定等于 Lane-Banked；Bank 也可用于其他多实例数据。

Lane-Banked 常见映射：

```text
Bank 0 → Lane 1–8
Bank 1 → Lane 9–16
Bank 2 → Lane 17–24
Bank 3 → Lane 25–32
```

但不能对所有 Banked Page 无条件使用 Lane 公式，应根据具体 Page 定义解释。

## 7.5 PageMapping 寄存器

位于 Lower Memory：

```text
Byte 126：BankSelect
Byte 127：PageSelect
```

访问译码模型：

```c
if (addr < 0x80) {
    return lower_memory[addr];
}

offset = addr - 0x80;
if (page_is_banked(page_select)) {
    return banked_page[page_select][bank_select][offset];
}
return page[page_select][offset];
```

实际实现还必须检查 Page/Bank 是否被模块支持。

## 7.6 原子切换 Page Address

如果 Bank 发生变化，Host 应在一次从 Byte 126 开始的 WRITE 中同时写入：

```text
[BankSelect, PageSelect]
```

例如：

```text
WRITE Byte 126: [01h, 10h]
```

模块在 PageSelect 写入后处理完整的新 Page Address，避免暴露“新 Bank + 旧 Page”的半更新组合。

如果 Bank 不变、只切换 Page，可只写 PageSelect。

## 7.7 Unsupported Page Address

若 Host 写入的 `{Bank, Page}` 组合不受模块支持，模块会清除 PageSelect，使 Upper Memory 回到 Page 00h；BankSelect 可保留，但 Page 00h 为非 Banked，因此 Bank 值被忽略。

## 7.8 Page Remapping 与 stale data

PageMapping 写入成功，不等于 Upper Memory 已经完成切换：

```text
写 PageMapping
→ 模块内部执行 Page Remapping
→ 新 Upper Memory 内容可安全访问
```

切换期间，如果访问可能读到旧 Page 的 stale data，模块应拒绝该 ACCESS。为简化实现，模块也可以暂时拒绝 Lower Memory 访问，但 Lower Memory 的语义并未改变。

安全 Host 流程：

```text
WRITE PageMapping
→ TEST/等待模块准备好
→ READ/WRITE Upper Memory
```

## 7.9 常见误区

- 认为所有 Upper Page 都必须结合 Bank。
- 认为 Banked Page 表示所有 Bank 一定存在。
- 无条件将 Bank 解释为每 8 条 Lane。
- 把 PageMapping 写成功当成 Remapping 已完成。
- 切换期间返回旧 Page 数据，而不是拒绝可能访问 stale data 的请求。

## 7.10 自测

1. `0x30` 与 `0x90` 分别如何译码？
2. Page 10h/Bank 1/0x90 大致对应哪组 Lane？
3. 一个 8-Lane 模块能否正常访问 Bank 1？
4. Bank 从 0 切换到 1 时，为何必须同时写 BankSelect 和 PageSelect？
5. 写完 PageMapping 后为什么不能立即假设新 Page 可读？

<details>
<summary><strong>参考答案与解析</strong></summary>

<ol>
<li>
<p><code>0x30</code> 位于 <code>0x00–0x7F</code>，属于 Lower Memory，只按 Byte Address 静态解释；<code>0x90</code> 位于 <code>0x80–0xFF</code>，属于 Upper Memory，必须结合 PageSelect，若所选 Page 是 Banked，还要结合 BankSelect。</p>
</li>
<li>
<p>对 lane-banked 的 Page 10h，Bank 1 通常对应 <strong>Lane 9–16</strong>；<code>0x90</code> 是该页面实例中的具体 Byte Address。</p>
</li>
<li>
<p>通常不能。Page 是 Banked 只说明它允许多个实例，不代表当前模块实现了所有 Bank。只有 8 条 Lane 的模块通常只需 Bank 0；Host 必须依据模块能力确认 Bank 1 是否存在。</p>
</li>
<li>
<p>Bank 改变时，<code>{BankSelect, PageSelect}</code> 构成一个完整 Page Address。一次从 Byte 126 开始写入两者，可避免暴露“新 Bank + 旧 Page”的半更新组合。即使 Page 数值不变，也应与新 Bank 一起提交。</p>
</li>
<li>
<p>选择寄存器写入后，模块可能还需要执行内部 Page Remapping。完成前读取 Upper Memory 可能得到 stale data，因此 Host 应等待或用 <code>TEST()</code> 确认准备好；模块应拒绝可能访问旧数据的请求，而不是把旧数据当成新 Page 返回。</p>
</li>
</ol>

</details>
---

# M8 Page 00h–05h 管理页（学习中）

## 8.1 Page 00h：Administrative Information

一句话：

```text
Page 00h = “我是谁”
```

主要包含静态、只读的模块身份和基础特征：

- Vendor Name、OUI；
- Part Number、Revision；
- Serial Number、Date Code；
- Power Class / Max Power；
- Connector Type；
- Media Interface Technology；
- 基础 MCI 广告等。

Page 00h 由所有模块支持，包括 Flat Memory Module。

## 8.2 Page 01h：Advertising

一句话：

```text
Page 01h = “我会什么”
```

包含模块能力广告：

- Supported Pages；
- 状态和操作持续时间；
- 支持的 Controls、Flags、Monitors；
- Signal Integrity 控制能力；
- CDB Messaging 支持；
- Application Descriptor；
- Media Lane Assignment；
- 其他能力字段。

Paged Memory Module 必须支持 Page 01h。Host 不能通过改写能力广告“创造”模块不具备的功能。

## 8.3 Page 02h：Module and Lane Thresholds

一句话：

```text
Page 02h = “什么数值算异常”
```

包含模块定义的只读阈值，例如：

- 温度 High/Low Alarm 与 Warning；
- 电压阈值；
- Lane-related Monitor 阈值。

Host 读取它们来解释 Monitor 和 Flag，不能任意写入改变模块的异常判定标准。Paged Memory Module 必须支持 Page 02h。

## 8.4 Page 03h：User EEPROM

提供可选的 Host-writable EEPROM，可保存资产编号、部署标签和其他 Host 自定义持久化数据。

边界：

- Optional，必须先从 Page 01h 确认支持；
- 可写不代表无访问时序、写周期或范围限制；
- 应按 EEPROM 特性和规范要求处理持久化。

## 8.5 Page 04h：Laser Capabilities Advertising

用于广告可调激光器能力，例如支持的 Channel 范围。

```text
Page 04h 是能力广告页 ≠ 实时调谐命令页
```

普通不可调激光器模块可能不支持该 Optional Page。

## 8.6 Page 05h：Form-Factor-Specific Management Signals

用于管理特定 Form Factor 的专用管理信号。它不是所有模块通用的基本页面，也必须由 Page 01h 广告支持情况。

## 8.7 Optional Page 的正确访问流程

以 Page 03h 为例：

```text
读取 Page 01h Supported Pages Advertisement
→ 确认 Page 03h 被支持
→ 写 PageSelect 选择 Page 03h
→ 等待 Page Remapping 完成
→ 访问 Page 03h User EEPROM
```

不能一开始就直接访问 Optional Page。若形成 unsupported Page Address，模块可能清除 PageSelect，使 Upper Memory 映射回 Page 00h。

## 8.8 Page 00h–05h 速查表

| Page | 核心用途 | 记忆方式 | 支持属性 |
|---|---|---|---|
| 00h | Administrative / Vendor / 基础特征 | 我是谁 | 所有模块支持 |
| 01h | Advertising | 我会什么 | Paged Module 必需 |
| 02h | Module/Lane Thresholds | 什么算异常 | Paged Module 必需 |
| 03h | User EEPROM | Host 持久化数据 | Optional |
| 04h | Tunable Laser Capabilities | 激光器能调什么 | Optional |
| 05h | Form-Factor-specific Signals | 封装专用信号 | Optional |

## 8.9 当前待继续内容

- 完成 Optional Page 访问顺序题；
- 进一步比较各 Page 的静态/动态、RO/RW 属性；
- 做 Page 00h–05h 综合访问流程验收。

## 8.10 自测

1. Vendor Name、Application Advertisement、温度阈值分别在哪个 Page？
2. Host 资产标签、可调激光器能力、封装专用信号分别在哪个 Page？
3. 访问 Page 03h 前为什么必须先读 Page 01h？
4. 为什么 Page 04h 不能简单视为实时 Laser Control 页面？

<details>
<summary><strong>参考答案与解析</strong></summary>

<ol>
<li>
<p>Vendor Name 在 <strong>Page 00h</strong>；Application Advertisement 在 <strong>Page 01h</strong>；温度等 Module/Lane Threshold 在 <strong>Page 02h</strong>。可记为“我是谁 / 我会什么 / 什么算异常”。</p>
</li>
<li>
<p>Host 资产标签对应 <strong>Page 03h User EEPROM</strong>；可调激光器能力对应 <strong>Page 04h</strong>；Form-Factor-specific 管理信号对应 <strong>Page 05h</strong>。</p>
</li>
<li>
<p>Page 03h 是 Optional Page。Host 必须先从 Page 01h 的 Supported Pages Advertisement 确认模块实现了它，然后才能选择、等待 Remapping 并访问。若直接选择不支持的 Page，模块可能清除 PageSelect，使 Upper Memory 映射回 Page 00h。</p>
</li>
<li>
<p>Page 04h 的主要职责是广告模块支持的 Tunable Laser 能力和范围，回答“模块能调什么”。能力描述不等同于执行“立即调到某个 Channel”的运行控制；实时控制应遵守相应控制字段和流程，不能仅凭能力页完成。</p>
</li>
</ol>

</details>
---

# 9. M1–M8 跨模块串联

一次典型的 Host 管理流程可以串联为：

```text
1. 通过独立 CMIS 管理通道发现模块                    [M1]
2. 按 bit/field/enum 正确解释寄存器数据              [M2]
3. 用 MSL 感知信号，用 RAL TEST/READ/WRITE 访问       [M3]
4. 从广告中识别 Application，并规划 Data Path 资源     [M4]
5. 写 Staged、Apply、校验、Provision、Commission       [M5]
6. 根据 MSM 判断模块是否处于可管理/可运行状态          [M6]
7. 用 Lower/Upper + Page/Bank 正确定位寄存器            [M7]
8. 从 Page 00h–05h 获取身份、能力、阈值和可选功能       [M8]
```

## 9.1 FW 开发中的核心不变量

1. **管理可用性与高速业务可用性分离。**
2. **寄存器值必须按宽度、位号和枚举定义解释。**
3. **临时忙要重试，但必须有超时；非法访问不能靠等待解决。**
4. **Application 是模板，Instance/Data Path 才绑定资源和状态。**
5. **候选配置不得在校验完成前进入 Active 或硬件。**
6. **Transient State 必须有完成、超时或故障出口。**
7. **Upper Memory 的地址含义必须结合 Page，必要时结合 Bank。**
8. **Optional 功能必须先发现/广告，再访问或使用。**

---

# 10. 个人易错点清单

这些是学习过程中实际暴露、最值得重复检查的点：

- 不要把 `0x05` 看成 `0x50`。
- bit n 的 mask 是 `1U << n`。
- 多 bit 字段右移量是最低位编号，不是最高位编号。
- Mask 的 1 是“允许通过”，不是“强制置 1”。
- Reserved 不能由厂商自行赋义。
- `TEST()` 检查下一次 READ/WRITE 是否可接受，不是测试寄存器 bit。
- Application 与 Application Instance 不同。
- 并行独立 Data Path 不应共享 Lane。
- Provisioning 是 Staged→Active；Commissioning 是 Active→硬件。
- Apply 校验失败时既不能 Provisioning，也不能 Commissioning。
- `ModuleReady` 不表示所有 Data Path 已激活。
- Transient 不等于响应所有 Event；应按规范优先级处理。
- Banked Page 不表示所有 Bank 都存在。
- PageMapping 写成功不等于 Remapping 已完成。
- Page 02h Threshold 是模块定义的只读阈值，不是 Host 任意配置值。

---

# 11. 后续模块占位

- M9：Lane/Data Path 控制与状态页 10h/11h/18h/19h
- M10：VDM 与性能诊断
- M11：CDB 消息机制
- M12：CDB 命令组与 Firmware Management
- M13：高级特性
- M14：时序与 MCI 物理层
- M15：初始化、诊断和网络路径实战
- M16：综合考核与跨版本兼容

每个模块学习后，将继续按以下模板扩充：

```text
定义
→ 工程原因
→ 正常流程
→ 失败/边界流程
→ Firmware 实现提示
→ 常见误区
→ 自测题
```