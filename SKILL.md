---
name: skill-memory
agent_created: true
description: 通用的「踩坑经验沉淀」协议与模板，适用于任何 skill 的自我记忆与复用。当 agent 在任意任务/skill 执行中踩坑、被纠正、反复试错、或发现可复用「黄金路径」后，按本协议把经验沉淀为三层记忆（机器层=确定性配置、经验层=agent 读的教训、流程层=流程本身），并带质量门槛、去重、进化评分与自我更新；也用于技能进化收口（ledger.json 直改、文档哈希与索引同步）和技能膨胀审计。目标是「试错只付一次」：第一次允许踩坑，第二次复用记忆直接走通。Use when finishing any task/skill and needing to persist lessons so the same flow never repeats trial-and-error, when asked to set up/refine the memory-sedimentation convention itself, or when auditing the skill-evolution ledger.
---

# Skill Memory — 通用踩坑沉淀协议

> **目标**：让「试错只付一次」。第一次允许踩坑，第二次复用记忆直接走通。
> **本 skill 是方法层（L2）**：定义协议与模板。全局纪律在 `~/.dsh/AGENTS.md`（DSH 宿主每次会话自动加载；其他宿主由各自的全局记忆/系统提示承担同等角色）；各领域的实际记忆落在各自 skill 内（L3）。

## 定位：三层记忆模型

| 层 | 读者 | 存什么 | 加载方式 | 由谁维护 |
|---|---|---|---|---|
| 机器层 | 执行代码 | 确定性配置（选择器/参数/开关/字段模式） | 代码自动加载，无需 LLM | 代码自动 + agent 补 |
| 经验层 | agent | 代码表达不了的教训（反爬行为/结构套路/完整推理） | agent 主动读 | agent 手动 |
| 流程层 | agent | 流程本身怎么跑（裁决表/阶段/专项） | skill 载入 | agent 手动 |

判定原则：**同一知识只落一层为主**——能写成确定性配置的进机器层；是「教训/推理」的进经验层；只有「流程怎么跑」变了才改流程层。避免同一句话写三遍。

> **落地优先级（2026-08-16 补充，来自真实教训）**：发现「黄金路径/最优方案」时，**先落机器层（升级执行代码/确定性配置），再写经验层文档**。机器层由代码自动加载、无需 LLM 干预，是唯一"改了立刻生效"的层；只写经验层文档 = 未沉淀（下次执行仍是旧逻辑）。无执行代码（纯任务）时退而求其次落经验层。曾有 agent 把提速 2 倍的方案只写进 EXPERIENCE.md、删掉临时脚本、引擎仍是旧逻辑，直到用户要求才改代码——此坑由用户纠正后沉淀为本规则。

> **机器层更新决策（2026-08-16 用户要求新增）**：每次在机器层发现**更优执行方案 / 更好的执行脚本**时（例如：临时/测试代码解决了固定代码原有的问题），按以下决策执行：
> - **确定新方案更好**（已通过真实场景/冒烟验证、收益明确、风险可控）→ **直接更新固定脚本/固定代码**并验证，**不要等用户要求**（与"黄金路径先落代码"一致）；
> - **不确定**（收益/兼容性/影响面不明，或涉及共享引擎、可能影响其他流程）→ **列出改进点与利弊，直接询问用户是否更新**，得到答复后再动固定代码。
> 判断要点：新方案是否经过真实数据验证、影响范围（独立脚本 vs 共享引擎）、回退成本。两忌：一忌"只写文档不落代码"（上次教训），二忌"拿不准还擅自改共享代码"——拿不准就问。此规则同样适用于纯任务场景（无 skill 时更新对应的项目/全局脚本）。

> **本协议适用于所有场景，不限于 skill 执行**：纯 agent 任务（未用任何 skill）同样会反复试错，同样要沉淀。无 skill 归属时，经验层落全局/项目记忆（见下），机器层与流程层天然不适用（无执行代码、无 SKILL.md）。

## 何时触发沉淀（出现任一即必须执行，不得跳过）

1. 反复试错才解决（改了 ≥2 次才成功）
2. 用户纠正做法 / 表达不满 / 指出错误
3. 发现可复用「黄金路径」（第一次就做对、值得固化）
4. 遇到登录 / 反爬 / 结构变化等非显然的坑

## 归属判定（避免误归到最近用过的 skill）

- 先判断「这次踩坑属于哪个流程 / skill / 项目」，再决定写进哪里的记忆。
- **不要默认归属到「最近用过」的 skill**：同一窗口上半段用了 skill A、下半段是新任务踩坑，教训应落新任务对应位置（无 skill 则落项目/全局记忆），而非写进 A。
- 判断依据：坑发生在哪个流程的哪一步、和哪个 skill 的职责相关。

## 沉淀执行步骤

1. **读**：先读目标流程已有的机器层 + 经验层（skill 内或全局/项目记忆），了解现状、避免重复。
2. **判**：按上节判定归属；再判本次教训属于哪一层、是否过质量门槛。
3. **写**：按对应层模板写入，保留「现象 → 根因 → 修法」三段，并写「下次如何验证已避开」。
4. **验**：下次执行同一流程前，先查历史经验并报告「命中/避开了哪些已知坑」，命中即证明机制生效。

## 三层模板

### 机器层（有执行代码的 skill 才需要）
- 位置：`<skill>/<记忆目录>/<实体key>.json`（如 `playbooks/<host>.json`）
- 内容：确定性字段（选择器/参数/开关）+ `notes` 一行摘要
- 代码侧要求：
  - **自动加载**：流程开头按 key 查配置、只补空缺（不覆盖显式配置）
  - **自动学习**：成功后带质量门槛回写（如：条数≥N、平均文本≥L、字段填充≥P%）
  - 门槛不达标就不写，避免垃圾污染

### 经验层（所有场景都应维护，含非 skill 任务）
- **跟随宿主原则**：经验写在记忆所属宿主的实际位置，不硬编码单一宿主路径。机器层配置必须紧邻执行代码，同样跟随 skill 本体。
- 落点（按归属就近落）：
  - 有 skill 归属：`<该 skill 目录>/EXPERIENCE.md`（经验多时拆 `<skill>/memory/` 分文件）
  - 项目专属：`{workspace}/.agent-memory/EXPERIENCE.md`（宿主中立，任何 agent 都读写同一处）
  - 跨项目/跨宿主通用（**共用记忆池**）：`~/.agent-memory/EXPERIENCE.md`
- 路径纪律：`.agent-memory` = skill-memory 专用（全局池 + 项目池，命名对称）；`{workspace}/.workbuddy/memory/` 是 WorkBuddy 自身记忆（日结/MEMORY.md），**不与 skill-memory 项目落点混用**，不要把经验写进宿主私有目录（`~/.claude/`、`~/.workbuddy/`、`~/.dsh/`）。
- **DSH 宿主本机的两个额外落点（指针，细节不在此展开）**：
  - **本机踩坑总库** = Mnemon Document `e62c84e5-8791-4b56-aba9-a6ee0eddc788`「本机环境事实与踩坑总库（v2）」，按 `## §N` 分节 + 顶部关键词索引。**别整篇读**：先 `grep '^## §'` 拿节号行号，再 `read` 带 `offset`/`limit` 只读命中那节。环境事实、插件坑、工具边界一律落这里。
  - **直改该文档磁盘文件后必须按顺序跑两步**（顺序不能反，2026-10-31 实测）：
    1. `node F:\despseekharness\xhs-grab\_fix_doc_hash.js "<md完整路径>" "<description>"` —— 重算 `content_hash` / `updated_at`；
    2. `node F:\despseekharness\xhs-grab\_sync_doc_index.js "<md完整路径>"` —— 把 hash / size / updatedAt / description 写回 `documents/index.json` 并 `revision+1`。
  - **参数坑**：`_sync_doc_index.js` 必须传**完整 `.md` 路径**（或能唯一匹配的长 id）。传短 id 直接报 `index.json 里找不到 id: d0398992`；换完整路径才成功（2026-10-31 实测）。
  - **新增小节**：加了 `## §N` 必须同时在该文档顶部关键词索引表补一行指向它，否则下次定位不到。
- 查询顺序（执行流程前 / 写入前查重）：**共用池 → 项目 → skill 本体**。
- 旧路径 `~/.dsh/memory/`、`<项目根>/memory/EXPERIENCE.md` 已废弃（2026-09-19）：遇到旧文件迁移到上述落点后在原位留一行指向新路径。
- 格式：表格条目 `| 场景 | 现象 | 对策（已实现/下次怎么做） |`，最小示例：
  `| 站点X搜索接口 | 间隔<10min 连续调用返回空白、不报错 | 间隔≥30min、单日≤4次；改走频道页绕开（已实测） |`
- 写「为什么」，不只写「是什么」

### 流程层（流程逻辑变化才动）
- 位置：`<skill>/SKILL.md`
- 只改裁决表/阶段/专项规则，不写具体站点/实例细节
- **修改前必须询问用户**（用户 2026-09-19 规则）：改任何 skill 的 `SKILL.md`（含本文件）前，说明 ①打算改什么 ②落点 ③为什么值得改，获明确同意再写。**经验层（EXPERIENCE.md）与机器层配置无需询问，直接写**。

## 质量门槛（不达标不写）

- 具体可复现（「现象 → 根因 → 修法」齐全，非空话）
- 非一次性偶然（大概率再遇到）
- 不重复（先查，重复则合并/更新旧条目）
- 脱敏（无 cookie 值、账号、token、密码）

## 进化与防腐化（记忆的自我更新）

- **去重**：先查再写；重复则更新旧条目而非新增。
- **淘汰**：过时/被推翻的经验标注 `[过时]` 或删除，不靠堆量。
- **评分（可选）**：给条目记「命中次数」，长期不命中的降级归档。
- **别让沉淀变成建技能（2026-10-31 用户定）**：踩坑经验**默认不建新 skill**。本协议的流程层写着「只有流程怎么跑变了才改流程层」，而绝大多数踩坑是经验层内容 —— 把它写成 `SKILL.md` 就是层次错配，会同时付「每回合技能目录成本」和「同一句话存两处」的代价。
  - **新建** skill 必须**同时**满足：①是可复跑的多步命令链、②带可断言的验证关、③确实没有归属 skill 可改、④不属于单一项目的专属流程。
  - **改已有 skill** 只需满足 ①②③；「改进已有技能的执行问题」是首选动作（正例：`xiaohongshu-grab` 的 v2 改造走 update，没有新建）。
  - 项目专属的流程与数字 → 项目档案 Document + 项目级技能目录（`<project>/.dsh/skills`），**不要进全局 `~/.dsh/skills`**。
  - 已落进 skill 的踩坑经验，发现重复时应**回退到记忆层**（总库 §N / `EXPERIENCE.md`），再把该技能移入回收站，而不是留着两份。
- **方法自更新**：若发现更好的沉淀方式，更新**本 SKILL.md**（先询问用户确认）+ 对应宿主的全局纪律文件（DSH 为 `~/.dsh/AGENTS.md`），让所有 skill 受益。

## 本机技能进化收口（可复跑执行链）

> 用户 2026-10-31 定：经验默认不建技能，只优化已有技能。一次「误建的技能回退 + 账本对齐」按下面几段跑，每段都有验证关。

### 1. 账本手术：先 dry 再 apply
- 不手点 GUI、不逐条调 API：写一次性 node 脚本，只读写 `C:\Users\sun\.mnemon\skills\ledger.json` 与同目录 `evals.json`，一轮完成 dismiss / adopt / invalidate。
- 脚本必须支持 `--dry`：只打印将要执行的动作与新旧内容长度，不落盘。
- 顺序（2026-10-31 实测：`-migrate` 脚本 dry 两次 → apply 一次）：
  ```powershell
  cd F:\despseekharness\xhs-grab
  node _migrate_skills_YYYYMMDD.js --dry   # 预演：dismiss/adopt/invalidate 与长度变化
  node _migrate_skills_YYYYMMDD.js         # 与预演一致才 apply
  ```
- apply 会打印 `version: <skill> original <id>` / `version: <skill> evolution <id>` / `adopt: <skill> ... -> draft <id>`（draft id 每次 apply 都会变）；记下这几个 id，下一步要核对。改了 `SKILL.md` 后要重跑 dry + apply，draft id 会再次更新——**以最后一次 apply 的输出为准**。
- **版本 hash 校验（apply 后必做）**：对当前 `SKILL.md` 算 sha256 前 16 位十六进制 + 字节数，与账本中该技能 version 条目的 hash / 字节数比对，必须 `match=true`；adopt 的 draft 内容 hash 也要与当前文件一致（不一致说明脚本读的是旧内容或编辑没落盘）。
  ```powershell
  node -e "const fs=require('fs'),crypto=require('crypto');const c=fs.readFileSync('C:/Users/sun/.agents/skills/skill-memory/SKILL.md','utf8');console.log(crypto.createHash('sha256').update(c).digest('hex').slice(0,16),Buffer.byteLength(c))"
  ```
- 收尾看账本：`generation` 已递增，目标建议状态由 `pending` 变为 `adopted` / `dismissed` / `invalidated`。
  ```powershell
  node -e "const fs=require('fs');const j=JSON.parse(fs.readFileSync('C:/Users/sun/.mnemon/skills/ledger.json','utf8'));console.log('generation',j.generation);for(const s of j.suggestions)console.log(s.status,'|',s.action,'|',s.skillName,'| conf='+s.confidence);"
  ```

### 2. 文档改完的两步同步（顺序不能反）
1. `node _fix_doc_hash.js <md完整路径> "<description>"` → 打印新的 `content_hash` 与 `updated_at`，确认已变；
2. `node _sync_doc_index.js <md完整路径>` → 应看到 `文件大小 / contentHash / updatedAt / revision` 的变化，`revision` 递增。
- 短 id 会失败（`index.json 里找不到 id`），别猜，直接给完整路径。
- 收尾抽查索引与磁盘一致（遍历 `documents/index.json`，逐条确认 `relativePath` 文件存在且 hash 对得上）。

### 3. 热记忆禁区：content 不能含 §
- `mnemon_runtime_memory` 的 `content` / `old_text` 里**不能出现 `§`**：它是保留的条目分隔符，会被直接拒绝（`content must not contain the reserved § entry delimiter`）。
- 要提节号就改写说法（「26 个小节」「报告排错取证法与 Tab 清单漂移」这类），把 `§N` 留在踩坑总库文档里；被拒后改写重试即可，不用换工具。

### 4. allowCreate=false 之后：只改不建
- 本机 `cordis.patch.yml` 的 `skills` 段已是 `allowCreate: false` + `allowUpdate: true` + `autoBacktest: true` + `reviewThreshold: 9` + `idleSeconds: 20` + `cooldownSeconds: 150`：Mnemon 自动复盘**只会提 update 建议**。
- 看到 create 建议 = 开关之前遗留的，逐条按判据处理（能落记忆就 dismiss / invalidate），不要顺手建。
- 确实需要新技能 → 由人明确要求后再建（临时开 `allowCreate` 或手工建），事后复原开关。

### 5. 技能膨胀审计（账本 → 判层）
1. 读账本：`generation` + 每条 suggestion 的 `status` / `action` / `skillName` / `confidence`，算出 create : update 比例（命令见 §1 末尾）。
2. 看 drafts 的 `targetRoot`：项目专属内容落到全局根（如 `user-dsh`）就是落层错误，应指向项目根 / 项目级技能目录。字段名以账本实际结构为准，先 `Object.keys(drafts[0])` 打印一次再取值。
3. 每条套三问：①是否可复跑的多步命令链？②是否单一项目专属？③记忆层（踩坑总库某节 / `EXPERIENCE.md`）是否已有？
   - 三问全过且确实无归属技能可改 → 才允许 create；
   - 其余一律 update 或落记忆档案，**依据要指到具体节号/条目**，不凭印象。
4. 结论写成表：`建议 | action | conf | 落层结论 | 依据`。

## 验证闭环

- 下次执行同一流程前，agent 先查历史经验、报告「命中/避开了哪些已知坑」，命中即证明机制生效。
- 若又踩了本可避开的坑 → 说明「没读记忆」或「经验写得不可用」→ 修经验条目 + 强化触发条件。
- 收口类操作（账本 / 文档同步）按各段自带的验证关核对：版本 hash `match=true`、索引 `revision` 递增且与磁盘一致、账本 `generation` 递增且建议状态已结。

## 边界

- 敏感信息不入记忆：cookie 值/账号/token/密码一律脱敏或外置（指向独立文件而非内联）。
- 记忆是「给下次的自己」的结论，不是流水账日志：写结论不写过程。
