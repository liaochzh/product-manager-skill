# Mermaid 图表速查

## 一、先选图，再画

| 要表达什么 | 用哪种图 | 关键要求 |
|-----------|---------|---------|
| 系统由哪些层/服务组成 | flowchart TB + subgraph | 分层不超过 4 层，每层只放本层职责 |
| 一条业务怎么走、哪里会失败 | flowchart TD | 判断节点用 `{}`，失败分支必须画出来 |
| 对象有哪些状态、怎么流转 | stateDiagram-v2 | 每个状态要有出口，终态标 `[*]` |
| 多方交互顺序、接口调用 | sequenceDiagram | 写明失败分支（alt/else），不画没有交互的参与者 |
| 用户体验的好坏起伏 | journey | 评分 1-5，只画 5-8 个关键步骤 |
| 数据结构与关系 | erDiagram | 标 PK/FK/UK，关系写清一对多方向 |
| 排期与依赖 | gantt | 有外部承诺才用；内部迭代用 Now/Next/Later |
| 评审/上线流程 | flowchart TD | 每个判断都要有「否」的回路 |

## 二、模板

产品架构图（分层）：

```mermaid
flowchart TB
    subgraph 客户端层
        WEB["Web端"]
        APP["App端"]
    end
    subgraph 网关层
        GW["API网关"]
    end
    subgraph 服务层
        USER["用户服务"]
        ORDER["订单服务"]
        PAY["支付服务"]
    end
    subgraph 数据层
        DB[("MySQL")]
        REDIS[("Redis")]
        MQ["消息队列"]
    end
    WEB & APP --> GW
    GW --> USER & ORDER & PAY
    USER & ORDER --> DB
    ORDER --> MQ
```

登录流程：

```mermaid
flowchart TD
    A([开始]) --> B[/输入手机号/]
    B --> C{格式校验}
    C -->|错误| D[提示格式错误]
    D --> B
    C -->|正确| E[获取验证码]
    E --> F[输入验证码]
    F --> G{验证正确?}
    G -->|错误| H[提示验证码错误]
    H --> F
    G -->|正确| I[登录成功]
    I --> J([跳转首页])
```

订单状态流转：

```mermaid
stateDiagram-v2
    [*] --> 待支付
    待支付 --> 已取消: 用户取消/超时
    待支付 --> 待发货: 支付成功
    待发货 --> 配送中: 商家发货
    配送中 --> 待收货: 物流送达
    待收货 --> 已完成: 用户确认
    待收货 --> 退货中: 申请退货
    退货中 --> 已退款: 退款成功
    已完成 --> [*]
```

迭代甘特图：

```mermaid
gantt
    title 产品v2.0迭代计划
    dateFormat YYYY-MM-DD
    section 产品设计
    需求调研       :d1, 2026-01-01, 14d
    PRD撰写        :d3, after d1, 10d
    section 开发
    核心功能开发   :d6, after d3, 30d
    辅助功能开发   :d7, after d6, 15d
    section 测试
    功能测试       :d9, after d6, 20d
    回归测试       :d10, after d9, 7d
    section 上线
    灰度发布       :d11, after d10, 3d
    全量发布       :d12, after d11, 2d
```

注册时序图：

```mermaid
sequenceDiagram
    participant U as 用户
    participant FE as 前端
    participant BE as 后端服务
    participant DB as 数据库
    U->>FE: 填写注册信息
    FE->>BE: POST /api/register
    BE->>DB: 查询是否已注册
    DB-->>BE: 返回结果
    alt 已注册
        BE-->>FE: 错误：手机号已注册
        FE-->>U: 显示错误提示
    else 未注册
        BE->>DB: 创建用户记录
        DB-->>BE: 返回用户ID
        BE-->>FE: 注册成功
        FE-->>U: 跳转验证页
    end
```

用户旅程图：

```mermaid
journey
    title 用户购物流程
    section 购物流程
      搜索商品: 5: 用户
      浏览详情: 3: 用户
      加入购物车: 5: 用户
      结算: 4: 用户
      支付: 3: 用户
      下单成功: 5: 用户
```

ER 图：

```mermaid
erDiagram
    USER {
        bigint id PK
        string mobile UK
        string email UK
        tinyint status
    }
    ORDER {
        bigint id PK
        bigint user_id FK
        string order_no UK
        decimal total_amount
        tinyint status
    }
    USER ||--o{ ORDER : places
```

PRD 评审流程：

```mermaid
flowchart TD
    A([PM起草PRD]) --> B["内部评审<br/>PM+设计+开发"]
    B --> C{评审通过?}
    C -->|否| D[修改PRD]
    D --> A
    C -->|是| E[技术方案设计]
    E --> F[开发任务分解]
    F --> G["Sprint Planning<br/>纳入迭代"]
    G --> H([PRD归档])
```

## 三、避坑

- 中文标签含空格、斜杠、括号时加双引号：`A["订单 / 支付"]`，否则部分渲染器解析失败。
- 同一行用 `&` 连接多个节点（`WEB & APP --> GW`），不要写两个箭头。
- 节点 ID 用英文短名，显示文本放引号里；ID 里不要出现中文和空格。
- `stateDiagram-v2` 中文状态名可直接用，但转移标签必须跟在冒号后。
- `gantt` 的 `after d1` 引用的是任务 ID，不是任务名；`dateFormat` 与日期写法要一致。
- `journey` 的评分是 1-5，冒号数量不能多也不能少。
- 图表最多 20 个节点；超过就拆成两张，别在一张图里讲完所有事。
- 图只是辅助：每张图下面补一句结论，别让读者自己解读。
