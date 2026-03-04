---
name: uml-design
description: UML 图表设计和绘制。当用户需要创建系统架构图、类图、时序图、用例图或其他 UML 图表时使用此技能。
---

# UML 图表设计

## 功能说明
此技能专门用于 UML(统一建模语言)图表的设计和绘制,包括:
- 系统架构设计
- 类图和对象图
- 时序图和协作图
- 用例图和活动图
- 状态图和部署图
- 组件图和包图

## 使用场景
- "绘制用户登录的时序图"
- "创建系统架构图"
- "设计数据库 ER 图"
- "绘制业务流程图"
- "创建用例图展示系统功能"

## 工作流程
1. 理解需求：分析用户想要的图表类型和内容
2. 生成mermaid：根据需求生成对应的 mermaid 代码
3. 返回给用户对应的 mermaid 代码

## UML 图表类型

### 1. 类图(Class Diagram)
用于展示系统中的类、属性、方法及其关系。

```mermaid
classDiagram

class User {
  -Long id
  -String username
  -String email
  -String password
  +login() boolean
  +logout() void
  +updateProfile() void
}

class Order {
  -Long id
  -String orderNumber
  -Decimal totalAmount
  -OrderStatus status
  +create() void
  +cancel() void
  +pay() boolean
}

class Product {
  -Long id
  -String name
  -Decimal price
  -Integer stock
  +updateStock(quantity) void
  +getPrice() Decimal
}

User "1" --> "*" Order : places
Order "*" --> "*" Product : contains
```

**关系类型**:
- **关联(Association)**:实线,表示类之间的关系
- **聚合(Aggregation)**:空心菱形,表示整体-部分关系
- **组合(Composition)**:实心菱形,表示强整体-部分关系
- **继承(Inheritance)**:空心三角箭头,表示is-a关系
- **实现(Realization)**:虚线空心三角箭头,表示接口实现
- **依赖(Dependency)**:虚线箭头,表示使用关系

### 2. 时序图(Sequence Diagram)
展示对象之间的交互顺序和消息传递。

```mermaid
sequenceDiagram
    actor User
    participant Browser as Web Browser
    participant API as API Server
    participant DB as Database

    User->>Browser: 输入登录信息
    Browser->>API: POST /api/login
    activate API

    API->>DB: 查询用户信息
    activate DB
    DB-->>API: 返回用户数据
    deactivate DB
    
    rect rgba(0,255,0,0.1)
    alt 验证成功
        API-->>API: 生成 JWT Token
        API-->>Browser: 返回 Token
        Browser-->>User: 登录成功
    else 验证失败
        API-->>Browser: 返回错误信息
        Browser-->>User: 显示错误提示
    end
    end

    deactivate API
```

**元素说明**:
- **Actor**:参与者(用户或外部系统)
- **Participant**:对象或组件
- **Message**:消息传递(同步/异步)
- **Activation/deactivate**:对象激活期间
- **Alt/Opt/Loop**:条件、可选、循环（使用不同rgba背景色区分alt/opt/loop块)


### 3. 用例图(Use Case Diagram)
展示系统功能和参与者之间的关系。

```mermaid
flowchart LR

User((用户))
Admin((管理员))

subgraph 电商系统
    UC1([浏览商品])
    UC2([搜索商品])
    UC3([添加到购物车])
    UC4([下单购买])
    UC5([支付订单])
    UC6([管理商品])
    UC7([查看订单])
    UC8([处理退款])
end

User --> UC1
User --> UC2
User --> UC3
User --> UC4
User --> UC5
User --> UC7

Admin --> UC6
Admin --> UC7
Admin --> UC8

UC4 -. include .-> UC3
UC5 -. include .-> UC4
```

**关系类型**:
- **关联**:参与者与用例之间的关系
- **包含(include)**:必须执行的用例
- **扩展(extend)**:可选执行的用例
- **泛化**:用例之间的继承关系

### 4. 活动图(Activity Diagram)
展示业务流程或算法的执行流程。

```mermaid
flowchart TD

Start([开始])
A[用户提交订单]
B{库存充足?}

C[锁定库存]
D[创建订单]

E[发送确认邮件]
F[更新库存]
G[记录日志]

H[等待支付]
I{支付成功?}

J[确认订单]
K[安排发货]

L[取消订单]
M[释放库存]

N[提示库存不足]

End([结束])

Start --> A --> B
B -- 是 --> C --> D --> E
D --> F
D --> G
E --> H
F --> H
G --> H

H --> I
I -- 是 --> J --> K --> End
I -- 否 --> L --> M --> End

B -- 否 --> N --> End
```

### 5. 状态图(State Diagram)

```mermaid
stateDiagram-v2

[*] --> 待支付 : 创建订单

待支付 --> 已支付 : 支付成功
待支付 --> 已取消 : 超时/用户取消

已支付 --> 配货中 : 开始配货
配货中 --> 已发货 : 发货

已发货 --> 已完成 : 确认收货
已发货 --> 退款中 : 申请退款

退款中 --> 已退款 : 退款成功
退款中 --> 已发货 : 拒绝退款

已完成 --> [*]
已取消 --> [*]
已退款 --> [*]
```

### 6. 组件图(Component Diagram)

```mermaid
flowchart TB

subgraph 前端层
    Web[Web 应用]
    Mobile[移动应用]
end

subgraph API层
    Gateway[API Gateway]
    Auth[认证服务]
end

subgraph 业务层
    UserService[用户服务]
    OrderService[订单服务]
    ProductService[商品服务]
    PayService[支付服务]
end

subgraph 数据层
    DB[(MySQL)]
    Cache[(Redis)]
    MQ[(消息队列)]
end

Web --> Gateway
Mobile --> Gateway

Gateway --> Auth
Gateway --> UserService
Gateway --> OrderService
Gateway --> ProductService

OrderService --> PayService
OrderService --> DB
UserService --> DB
ProductService --> DB

UserService --> Cache
ProductService --> Cache

OrderService --> MQ
PayService --> MQ
```

### 7. 部署图(Deployment Diagram)

```mermaid
flowchart TB

subgraph 用户设备
    Browser[浏览器]
    App[移动 App]
end

subgraph 负载均衡器
    Nginx[Nginx]
end

subgraph 应用服务器集群
    Node1[Node.js 实例 1]
    Node2[Node.js 实例 2]
end

subgraph 数据库服务器
    Master[(MySQL 主库)]
    Slave[(MySQL 从库)]
end

subgraph 缓存服务器
    Redis[(Redis 集群)]
end

subgraph 文件存储
    OSS[OSS/S3]
end

Browser --> Nginx
App --> Nginx

Nginx --> Node1
Nginx --> Node2

Node1 --> Master
Node2 --> Master
Master --> Slave

Node1 --> Redis
Node2 --> Redis

Node1 --> OSS
Node2 --> OSS
```

## 注意事项
- 选择合适的图表类型
- 保持图表简洁清晰
- 使用一致的命名规范
- 及时更新文档
- 考虑图表的可读性
- 避免过度设计
- 使用版本控制管理图表文件
- 与团队保持沟通