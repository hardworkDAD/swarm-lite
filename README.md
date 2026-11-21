# SWML (Swarm Lite)

给 [Antigravity](https://antigravity.google/) 用的轻量化主从分工技能（输入 `/swml` 触发）。

一句话总结：**让聪明的主模型只当“大脑”——聊需求、定方案、最后看 diff 验收；把翻代码、改代码、跑编译、跑测试这些又长又吵的脏活累活，全都甩给独立的 Hydrogen Worker 去干。**

> **为什么叫 `swml`？**  
> `swml` 是 **Sw**ar**m** **L**ite 的缩写：敲 `/swml` 只有 4 个字母顺手好记，意思就是“精简版 Swarm”。

---

## 为什么做这个？

平时用 AI 写稍微复杂点的需求，经常会遇到两个头疼问题：

1. **主模型上下文很快被垃圾日志塞满**：翻十几个文件、改几轮代码、跑编译报错几百行，贵又聪明的主模型（Neon / Carbon / Opus 等）上下文一下就脏了，越聊越慢、越聊越糊涂，还特别烧额度。
2. **完整的 Swarm 又太重**：普通开发任务动不动拉起旁白、监控、专职审查员一堆角色，反而啰嗦、耗时、费 Token。

**SWML 的做法很直接：**

- **大脑和手脚分开**：主会话只管和你对齐需求、设计方案和最终验收；查大文件、改代码、跑构建和测试、修报错、做 Git 提交，全部隔离在 `gemini-4-hydrogen-high` Worker 里完成，满屏日志不会污染主对话。
- **不养闲人**：只建真正干活的 Worker，严禁创建旁白（narrator）、监控（monitor）之类的陪跑角色。
- **干完一阶段就换干净的 Worker**：先派只读 Worker 查清楚代码，查完直接销毁；真正写代码时再开全新的 Worker，不把查代码时的废话带进编码阶段。
- **干完由主模型亲自验收**：Worker 跑完测试后不会直接结束，主模型会自己看一遍 `git diff`，专门盯着有没有跳过测试、放松断言、吞异常这些偷懒行为；真碰到改 2~3 次还过不了的硬骨头，Worker 会自动停手，交给主模型接管。
- **小改动不折腾**：只是改改文档，或者 5 行以内一眼看穿的小修改，主模型直接顺手改了，绝不杀鸡用牛刀。

---

## 工作流程长这样

```text
你输入 /swml + 需求
  ↓
主会话（大脑）：和你确认需求、定方案
  ├─ 需要摸清代码？先派只读 Worker 去查，结论回来就销毁
  ↓
Hydrogen Worker（手脚）：拿到完整方案，自己去改代码、跑编译、跑单测
  ├─ 编译报错最多修 3 次，逻辑/断言失败最多修 2 次，超限自动停手求助
  ↓
主会话（验收）：看一遍 git diff 确认没问题 ➔ 让 Worker 提交代码 ➔ 销毁清场
```

---

## 安装方式

选一种你顺手的就行：

### 方式一：装成全局 Skill（推荐，最省事）

```bash
git clone https://github.com/hardworkDAD/swarm-lite.git
mkdir -p ~/.gemini/config/skills
cp -r swarm-lite/skills/swml ~/.gemini/config/skills/swml
```

### 方式二：装成 Antigravity 插件

```bash
git clone https://github.com/hardworkDAD/swarm-lite.git
agy plugin install ./swarm-lite
```

### 方式三：只在某一个项目里用

把仓库里的 `skills/swml` 复制到目标项目的 `.agents/skills/swml/` 目录下即可。

---

## 怎么用

- **平时不会乱触发**：这是手动开关，日常聊天完全不会自动启动。
- **干重活时开启**：在 Antigravity CLI 或 IDE 对话里输入 `/swml`（或者直接说“用主从隔离模式”）就会生效。
- **能和其他技能一起用**：它只管“谁来干活、怎么隔离上下文”，和你平时用的 `/tdd`、设计规范、Code Review 技能完全不冲突。
- **想换执行模型？** Worker 默认用 `gemini-4-hydrogen-high`。临时想换直接在对话里说一声就行；想永久换，直接改 [`skills/swml/SKILL.md`](skills/swml/SKILL.md) 里的模型名。

---

## 目录说明

```text
swarm-lite/
├── skills/swml/SKILL.md   # 核心技能文件（真正起作用的就是它）
├── plugin.json            # Antigravity 插件信息（支持 agy plugin install）
├── assets/logo.svg        # 插件图标
├── README.md
└── LICENSE                # MIT 协议
```

---

## License

[MIT](LICENSE)
