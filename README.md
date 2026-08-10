# product-map-workflow

一个面向 Codex 的精简任务执行 Skill：先读规则与产品图谱，明确目标，复用已有方案，只做必要改动，并在完成后同步更新产品图谱。

## 适用场景

项目、产品、页面、接口、代码、内容及其他需要持续遵循统一工作流的任务。

## 核心流程

1. 读取 Skill 规则。
2. 读取最新 product-map 的相关节点。
3. 锁定核心目标和最小范围。
4. 优先复用已有方案。
5. 极简实现并完成验证。
6. 同步更新 product-map。
7. 简洁交付可用结果。

## 安装

将整个目录复制到：

```text
~/.codex/skills/product-map-workflow/
```

至少保留：

```text
SKILL.md
agents/openai.yaml
```

## 许可证

MIT License
