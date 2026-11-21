# SWML (Swarm Lite)

面向 [Antigravity](https://antigravity.google/) 的轻量化主从隔离研发调度技能（触发指令：`/swml`）—— **主会话纯控制面 + 执行面 Hydrogen Worker**。

> **为什么叫 `swml`？**  
> `swml` = **Sw**ar**m** **L**ite。4 字母极简斜杠指令（`/swml`），精准体现“基于原生 `swarm`、零伴生角色开销、上下文信噪比隔离”的轻量化调度定位。

---

## 核心特性

1. **控制面与执行面按信噪比切分**
   - **主会话（控制面）**：保留高推理主模型（如 Neon / Carbon / Opus 等），专注需求交互、方案设计、局部抽检与最终只读 `git status` / `git diff` 审计，远离海量编译与测试日志污染。
   - **Hydrogen Worker（执行面）**：默认固定绑定 `gemini-4-hydrogen-high`，全权接管跨目录检索、批量改码、全量构建与单测、外部环境验证、本机联调部署及 Git 写操作。
2. **极简轻量化纪律**
   - 仅按需创建业务执行 Worker，严禁创建 `narrator`、`monitor`、专职审查员等伴生开销角色。
   - 无在途 Worker 时的纯文档改动或 `≤ 5` 行确定性微调由主会话直通完成，避免冷启动开销倒挂；主从同模时自适应收敛。
3. **看板原生无损共享与零空转启动**
   - 无需落盘临时协议文件，完整规格、接口签名、边界条件与绝对路径行号直接上板；多 Worker 通过看板 `#seq` 安全共享规格。
   - 内联【Worker 执行铁律】：绝对路径约束、`±40` 行按窗精读、测试隔离与反作弊守则、编译 3 次 / 算法 2 次硬熔断（`[CIRCUIT_BREAK]`）。
4. **按阶段换血与并发安全**
   - 跨阶段强制换血（`explore-*` ➔ `kill` ➔ `code-*`）与 `test-runner` 定期换血，彻底清理探索与构建缓存噪音。
   - 跨子系统按互斥文件分片并行改码 + 单实例串行收口构建，多清单逐单提交水位控制（`WIP ≤ 2`），终审落袋后再统一 `kill` 清场。

---

## 安装方式

### 方式一：作为全局 Skill 安装（推荐）

```bash
git clone https://github.com/hardworkDAD/swarm-lite.git
mkdir -p ~/.gemini/config/skills
cp -r swarm-lite/skills/swml ~/.gemini/config/skills/swml
```

### 方式二：作为 Antigravity Plugin 安装

```bash
git clone https://github.com/hardworkDAD/swarm-lite.git
agy plugin install ./swarm-lite
```

### 方式三：仅在单个项目仓库内启用

将本仓库的 `skills/swml` 复制到目标项目的 `.agents/skills/swml/` 目录即可随项目共享。

---

## 使用方法

本技能采用**手动触发**策略，日常对话不会自动激活，避免无谓消耗调度开销。

- 在 Antigravity CLI (`agy`) 或 IDE 对话中输入 `/swml`（或显式要求“启用主从隔离模式”）即可激活。
- 与会话中同时启用的其他设计、规范或工作流技能（如 `tdd`、`codebase-design`、`code-review` 等）正交叠加，互不覆盖。
- **自定义执行面模型**：默认执行面 Worker 绑定 `gemini-4-hydrogen-high`；如需临时切换模型，在对话中直接指定即可；如需长期更改默认执行模型，直接编辑 [`skills/swml/SKILL.md`](skills/swml/SKILL.md) 中的 `model` 字段。

---

## 仓库结构

```text
swarm-lite/
├── plugin.json             # Antigravity 插件清单（支持 agy plugin install / validate）
├── assets/
│   └── logo.svg            # 插件图标
├── skills/
│   └── swml/
│       └── SKILL.md        # /swml 主从隔离研发调度协议核心指令
├── README.md
└── LICENSE                 # MIT License
```

---

## License

[MIT](LICENSE)
