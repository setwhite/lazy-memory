# lazy-memory

一个"懒惰"的 AI 记忆库：被动记录、grep 检索、验证门控。

> 你没有存记忆的意识，就不会有记忆。

面向 AI 编码代理（pi 等支持 skill 的 CLI 工具）的被动记忆 skill。
核心设计：人触发写入、grep 语义检索、验证驱动失效。细节见 skill 内文档。

## 目录结构

```
lazy-memory/
├── SKILL.md                    # 触发时机与取用入口
├── references/PROTOCOL.md      # 协议总纲（唯一真相源）
└── scripts/
    ├── verify.sh               # 批量重跑 code 验证器
    └── status.sh               # 查询实体验证状态（门控）
```

## 安装（pi）

```bash
mkdir -p ~/.pi/skills
cp -r lazy-memory/lazy-memory ~/.pi/skills/
```

## License

[MIT](LICENSE)
