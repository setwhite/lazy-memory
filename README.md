# lazy-memory

一个"懒惰"的 AI 记忆库：被动记录、grep 检索、验证门控。

> 你没有存记忆的意识，就不会有记忆。

## 这是什么

lazy-memory 是面向 AI 编码代理（pi / Claude Code 等支持 skill 的 CLI 工具）的**被动记忆 skill**。
它管理本地 `.memory/` 实体记忆库——实体 + 描述 + 验证记录，全部是纯文本 Markdown。

核心立场：**记忆不是存储问题，是信任问题。**

- **写**：由人触发，绝不自作主张。你觉得值得记，才发提示词；大模型按协议写入。
- **找**：grep 直搜原始文本，怎么搜由大模型决定（语义 grep：同义词 + 上位词 + 中英文扩展，多轮补漏）。
- **存**：本地 md，一个实体一个文件（front-matter：id / kind / sources），正文只写可验证断言。
- **忘**：验证驱动失效。不改正文、只追加 failed 验证记录，门控自动排除——错误本身也是审计资产。

## 目录结构

```
lazy-memory/
├── README.md                    # 项目说明
├── LICENSE                      # MIT
└── skill/                       # skill 全部内容，整体复制即可安装
    ├── SKILL.md                 # 触发时机与取用入口
    ├── references/PROTOCOL.md   # 协议总纲：一切格式与规则的唯一真相源
    └── scripts/
        ├── verify.sh            # 批量重跑历史 code 验证器（L2 层）
        └── status.sh            # 查询实体最新验证状态（门控）
```

运行时记忆库（不随仓库分发，git 已忽略）：

```
.memory/
├── entities/          # 实体：一个文件一个
└── verifications/     # 验证流水账：只追加、不覆盖
```

## 安装（pi）

```bash
mkdir -p ~/.pi/skills
cp -r lazy-memory/skill ~/.pi/skills/lazy-memory
```

其他支持 skill 的代理参照其 skill 目录安装即可。

## 设计：四维对照

| 维度 | 常见实现 | lazy-memory 的选择 |
|---|---|---|
| 写什么 | 全量日志 / token 压缩 / 蒸馏提取 / 工具化让模型自判 | **人触发**，模型按协议落盘 |
| 怎么找 | 向量 embedding / BM25+RRF / grep / 认知控制 | **grep 直搜**，检索词模型定 |
| 存什么 | 扁平文件 / 分层 / 双记忆 / 知识图谱 | **本地 md**，实体 + 描述 + 验证记录分离 |
| 忘什么 | 衰减 / 受控覆写 / 学习式调度 | **验证驱动失效**：不删正文，门控排除 |

## 验证与门控

- **分层验证**：L0 文件结构 → L1 实体间矛盾 → L2 执行 code 命令 → L3 外部事实 → L4 问用户，每档独立可跳过。
- **门控三态**（取最新验证记录）：

| 最新记录 | 处置 |
|---|---|
| passed | 当事实用 |
| failed | 标"已失效"，不当事实用 |
| 无记录 | 标"未证实"，慎用 |

- **修正失效事实**：不改实体正文（可审计）；追加 failed 记录并写明推翻依据；事实仍有价值则另建实体。
- 验证记录只追加、不覆盖——全程可审计。

## 脚本

```bash
# 批量重跑所有历史 code 验证器，输出每个实体的通过/失败
./scripts/verify.sh

# 查询单个实体的最新验证状态（门控依据）
./scripts/status.sh <实体id>
```

记忆库根目录解析顺序：`$MEMORY_DIR` → git 仓库根 → 当前目录。

## 协议

所有格式与规则（实体文件格式、检索协议、验证分层、门控判定、修正流程）见 [references/PROTOCOL.md](references/PROTOCOL.md)，它是唯一真相源。

## License

[MIT](LICENSE)
