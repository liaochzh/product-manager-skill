# product-manager

一个用于 **软件产品经理深度咨询** 的 agent skill：需求分析与立项评估、PRD 撰写、需求质量审计、优先级排序、路线图、竞品分析、指标体系、数据分析、Mermaid 图表、H5 原型。

> 面向 opencode / Claude 一类支持 `SKILL.md` 的 agent 运行时。纯提示词实现，无依赖、无脚本、不联网也能用。

## 它解决什么问题

| 常见失败 | 本 skill 的做法 |
|---|---|
| 材料不足就敢下结论、打高分 | **三道闸门**：存在性校验（问题 / 用户 / 证据缺两项即拒绝评分）+ 框架门（方案偷渡、无成功指标、功能堆砌）+ 标注纪律 |
| 结论模棱两可，用户读完不能决策 | 决策档位可量化：GO / ITERATE / STOP，附理由与被放弃的选项 |
| PRD 写成长篇小说，读完不知道做什么 | 三档 PRD 模板（一页备忘 / 标准 / 完整），按材料完整度裁剪，超过 2 页还没决策即视为未决策 |
| 指标写成「提升体验」 | 结果可测：数值 + 方向 + 时限；附指标好坏对照与常见指标表演 |
| 需求不可测、抽象层级错位 | 18 条需求质量特征 + 模糊词表 + 可测化改写 |
| 优先级靠嗓门 | MoSCoW / RICE / ICE / Kano 评分锚点，切割线与理由必须写出来 |
| 推断当事实 | 统一标注 `[假设]` / `[待验证]` / `[待确认: X]` / `[缺数据]` / `[需埋点]` |

## 何时触发

用户需要产品经理级别支持：需求分析、立项评估、PRD 文档、需求质量审计、优先级与路线图、竞品分析、指标体系、数据分析、Mermaid 架构图/流程图/时序图/甘特图、H5 可交互原型。

工作模式按材料完整度自动切换：**引导式**（材料少，先问关键问题）→ **材料直灌**（材料足，直接产出）→ **最佳猜测**（不可提问时按假设推进并逐条标注）。

不负责：代码审查（用 review）、方案技术评审（用 design-review）、零提问快速出原型（用 product-prototype-generation）。

## 安装

```bash
# 方式一：整个目录放到 skills 根目录
git clone https://github.com/liaochzh/product-manager-skill.git
cp -R product-manager-skill ~/.config/opencode/skills/product-manager

# 方式二：软链（便于跟随仓库更新）
ln -s "$(pwd)/product-manager-skill" ~/.config/opencode/skills/product-manager
```

不同运行时的 skills 目录不同（例如 Claude Code 的 `~/.claude/skills/`），把本目录放进去即可，`SKILL.md` 的 frontmatter（`name` / `description`）就是触发依据。

## 结构

```
SKILL.md                    # 主体：触发与分流 → 工作模式 → 三道闸门 → 路由表 → 十阶段流程
                            #       → 交付契约 → 质量门 → 参考文件 → 知识速查 → 避坑 → 输出格式
references/prd-template.md          # PRD 模板（三档）、指标写法、红队评审、交付自检
references/requirements-quality.md  # 18 条需求质量特征、抽象层级、模糊词表、可测化改写
references/prioritization.md        # MoSCoW / RICE / ICE / Kano、评分锚点、切割线、路线图形态
references/idea-validation.md       # 存在性校验、竞争扫描、五维评分、GO/ITERATE/STOP、低成本验证
references/charts.md                # Mermaid 速查与选图决策
references/h5-prototype.md          # H5 单文件原型规范
```

参考文件**按需读取**，不必一次全读；`SKILL.md` 会按任务类型指向对应的一篇或两篇。

## 设计要点

- **先拒绝，再产出**：材料不足时不给评分、不写完整 PRD，先列出缺什么；同时可给一版标满 `[假设]` 的草稿，避免空转。
- **框架门只挑战一轮**：指出问题 + 给替代表述，然后按用户意愿继续，不连环追问；小问题就地标注并继续推进。
- **结论即交付物**：每个结论都写明代价与被放弃的选项，不让读者读完还要自己权衡。
- **可测化优先于修辞**：所有验收与指标都要求「数值 + 方向 + 时限」，不可测的表达一律改写或标注。

## License

MIT（见 `LICENSE`）。
